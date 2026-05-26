# 4390 Enterprise Network Security & Monitoring Capstone
*Group 15 | Anthony, Justin N, Justin W, Kelvin, Nathan, Ze*


## Project Objective
This repository contains the full documentation, configuration files, and verification for the design and implementation of a secure enterprise network. 
The infrastructure connects two branch offices and remote users, featuring Fortigate firewalls, centralized Splunk logging, and simulated cyber attack detection.

---

## Network Design & Architecture

### Topology Diagram
![Network Diagram](images/capstinenetworkdiagram.png)
> *`Network Diagram`*

### IP Addressing Scheme
The network utilizes a subnetted IP addressing scheme:
* **LAN:** `Site 1: 192.168.15.0/24, Site 2: 192.168.10.0/24`
* **DMZ:** `Site 1: 192.168.15.20, Site 2: 192.168.10.30`
* **Management:** `Site 1: 192.168.15.10/24, Site 2: 192.168.10.10/24`
* **Site to Site:** `Site 1: 1.1.15.1/24, Site 2: 1.1.15.2/24`

---
## VPN Implementation

### Site-to-Site VPN 
#### IPsec tunnel established between the FortiGate firewalls.

Site 1 Tunnel Up
![Site 1 Tunnel Up](images/Site1TunUP.png)
Site 1 Tunnel Configuration
![Site 1 Tunnel Up](images/VPNTunnelSite1.png)
Site 2 Tunnel Up
![Site 2 Tunnel Up](images/Site2TunnelUp.png)
Site 2 Tunnel Configuration
![Site 2 Tunnel Config](images/Site2TunnelConfig.png)
---
### Client-Based VPN (Remote Access)
Tunnel Up 
![Client Tunnel Up](images/Remote_Access_Tunnel.png)
Client Tunnel Config
![Client Config](images/Client_Tunnel.png)
Credentials for VPN User
![VPN User](images/VPNuser.png)
Connection in Windows 
![VPN User](images/VPNconnected.png)
Connection Properties 
![Connection Propertie](images/Windows_VPN_Connection_Properties.png)

### Server Deployment
Internal services hosted within the servers.

* **Apache Web Server**
  ![Apache Status](images/Apache_Status.png)
  ![Apache Server IP](images/Apache_server_ip.png)
* **SSH Server**
  ![Site 2 SSH Status](images/Site2SSHstatus.png)
  ![SSH IP](images/ssh_ip.png)
* **VPN Connection to Servers**
--
  Pings
  ![Pings](images/VPN_pinging_servers.png)
  SSH
  ![SSH](images/SSH_Connected.png)
  Website
  ![apache](images/web_server_connected.png)

---

## Centralized Logging & Splunk

All network components (Firewalls, Servers, VPNs) are configured to forward logs to a centralized Splunk instance for monitoring and correlation.

### Syslog Configuration
* **FortiGate:**
  Site 1 on UDP port 514
  ![Fortigate Syslogs](images/Site1syslogsettings.png)
  Site 2 on UDP port 514
  ![Site 2 Syslog Fortigate](images/site2syslogfortigate.png)
* **Apache & SSH Server:**
  Apache on UDP port 5515
  ![Apache Syslog](images/Apachesyslog.png)

### Splunk Logs
* **Syslogs in Splunk:**
Fortigate Syslogs
Site 1 
![Fortigate Syslogs](images/FortigateAlogs.png)
Site 2
![Fortigate Syslogs](images/VPNlogs.png)
VPN Logs
![Fortigate Syslogs](images/SSHlogs.png)
SSH Syslogs 
![Fortigate Syslogs](images/SSHlogs.png)
Apache2 Syslogs (Web Server)
![Apache2 Syslogs](images/apache2logs.png)
---



## Attack Simulation & Detection
To validate the security and monitoring capabilities, controlled attacks were simulated using a Kali Linux VM.
### DoS Protection
Before an attack is made a DOS policy is made to block unwanted traffic and to tell the firewall to drop uneeded malicous traffic, and so that it does not crash when being floodeed
![DOS Policy](images/dospolicy.png)
![DOS Protect 1](images/dosprotect1.png)
![DOS Protect 2](images/dosprotect2.png)

### Nmap Port Scan 
![NMAP](images/NMAPAttack.png)
![NMAP](images/Portscandash.png)
![NMAP](images/.png) fix this 

### ICMP Attack 
![ICMP](images/ICMPFlood.png)
![ICMP](images/icmpfloodfortigate.png)
![ICMP](images/icmpfloodsplunk.png) 

### UDP Attack 
![UDP](images/UDPFlood.png)
![UDP](images/udpfloodfortigate.png)
![UDP](images/udpfloodsplunk.png) 

### Fortigate Configuration Files Sanitized 
* Fortigate A
[FortiGate Configuration Backup (Raw)](FG-Group15-A_7-2_1762_202605261026.conf)
* Fortigate B
[FortiGate Configuration Backup (Raw)](FG-Group15-B_7-0_0667_202605261300.conf)

## Extra Items
--
Fortigate Automation
* [Fortigate Automation](fortigate_automation.py)
* [Fortigate Automation](Fortigate_automation.txt)
--
Splunk Rules
* [splunk](fortigate_splunk_correlation_rules.spl)
* [splunk](fortigate_splunk_correlation_rules)
--
Integration
* Did not implement   
