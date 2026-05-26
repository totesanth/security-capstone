#!/usr/bin/env python3
"""
FortiGate Automation Script - Group 15
Automates backup, log export, and health checks for FG-Group15-A and FG-Group15-B
Requires: paramiko, requests
"""

import paramiko
import requests
import json
import os
import datetime
import urllib3

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

FORTIGATES = [
    {"name": "FG-Group15-A", "host": "192.168.15.1", "port": 22, "api_port": 443},
    {"name": "FG-Group15-B", "host": "192.168.15.2", "port": 22, "api_port": 443},
]

SSH_USER = "admin"
SSH_PASSWORD = "your_password_here"
API_TOKEN = "your_api_token_here"

BACKUP_DIR = "./backups"
LOG_DIR = "./logs"
SPLUNK_HEC = "http://splunk-server:8088/services/collector"
SPLUNK_TOKEN = "your_splunk_hec_token_here"

os.makedirs(BACKUP_DIR, exist_ok=True)
os.makedirs(LOG_DIR, exist_ok=True)

timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")

def backup_config_ssh(fg):
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    try:
        client.connect(
            fg["host"],
            port=fg["port"],
            username=SSH_USER,
            password=SSH_PASSWORD,
            timeout=10
        )
        stdin, stdout, stderr = client.exec_command("show full-configuration")
        config_data = stdout.read().decode("utf-8")
        filename = f"{BACKUP_DIR}/{fg['name']}_{timestamp}.conf"
        with open(filename, "w") as f:
            f.write(config_data)
        print(f"[+] Backup saved: {filename}")
    except Exception as e:
        print(f"[-] Backup failed for {fg['name']}: {e}")
    finally:
        client.close()

def export_logs_api(fg):
    headers = {"Authorization": f"Bearer {API_TOKEN}"}
    log_types = ["traffic", "event", "security"]
    for log_type in log_types:
        url = f"https://{fg['host']}:{fg['api_port']}/api/v2/log/disk/{log_type}?lines=1000"
        try:
            resp = requests.get(url, headers=headers, verify=False, timeout=15)
            if resp.status_code == 200:
                data = resp.json()
                filename = f"{LOG_DIR}/{fg['name']}_{log_type}_{timestamp}.json"
                with open(filename, "w") as f:
                    json.dump(data, f, indent=2)
                print(f"[+] Logs saved: {filename}")
            else:
                print(f"[-] Failed to get {log_type} logs from {fg['name']}: HTTP {resp.status_code}")
        except Exception as e:
            print(f"[-] API error for {fg['name']} ({log_type}): {e}")

def send_to_splunk(event_data, sourcetype="fortigate:log"):
    payload = {
        "sourcetype": sourcetype,
        "event": event_data
    }
    headers = {"Authorization": f"Splunk {SPLUNK_TOKEN}"}
    try:
        resp = requests.post(SPLUNK_HEC, json=payload, headers=headers, timeout=5)
        if resp.status_code == 200:
            print("[+] Event forwarded to Splunk.")
        else:
            print(f"[-] Splunk HEC error: {resp.status_code} {resp.text}")
    except Exception as e:
        print(f"[-] Splunk forwarding failed: {e}")

def health_check_ssh(fg):
    commands = [
        "get system status",
        "get system performance status",
        "diagnose sys session stat",
        "get router info routing-table all",
    ]
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    try:
        client.connect(
            fg["host"],
            port=fg["port"],
            username=SSH_USER,
            password=SSH_PASSWORD,
            timeout=10
        )
        output_lines = [f"=== Health Check: {fg['name']} @ {timestamp} ===\n"]
        for cmd in commands:
            stdin, stdout, stderr = client.exec_command(cmd)
            result = stdout.read().decode("utf-8")
            output_lines.append(f"--- {cmd} ---\n{result}\n")
        filename = f"{LOG_DIR}/{fg['name']}_health_{timestamp}.txt"
        with open(filename, "w") as f:
            f.writelines(output_lines)
        print(f"[+] Health report saved: {filename}")
        send_to_splunk({
            "device": fg["name"],
            "event": "health_check",
            "data": "".join(output_lines)
        })
    except Exception as e:
        print(f"[-] Health check failed for {fg['name']}: {e}")
    finally:
        client.close()

if __name__ == "__main__":
    print(f"\n[*] Starting FortiGate automation - {timestamp}")
    for fg in FORTIGATES:
        print(f"\n[~] Processing {fg['name']} ({fg['host']})")
        backup_config_ssh(fg)
        export_logs_api(fg)
        health_check_ssh(fg)
    print("\n[*] Automation complete.")