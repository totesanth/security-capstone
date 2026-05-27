# CISA 4390 
# Enterprise Network Security & Monitoring Capstone
*Group 15 | Anthony, Justin N, Justin W, Kelvin, Nathan, Ze*

***

## Table of Contents
- [Project Objective](#project-objective)
- [Network Design & Architecture](#network-design--architecture)
- [VPN Implementation](#vpn-implementation)
- [Server Deployment](#server-deployment)
- [VPN Connection to Servers](#vpn-connection-to-servers)
- [Centralized Logging & Splunk](#centralized-logging--splunk)
- [Attack Simulation & Detection](#attack-simulation--detection)
- [Splunk Dashboard](#splunk-dashboard)
- [Configuration Files](#configuration-files)
- [Extra Items](#extra-items)

***

## Project Objective
This repository contains the full documentation, configuration files, and verification for the design and implementation of a secure enterprise network. 
The infrastructure connects two branch offices and remote users, featuring two Fortigate firewalls, centralized Splunk logging, and simulated cyber attack detection.

---

## Network Design & Architecture

### Topology Diagram
![Network Diagram](images/capstinenetworkdiagram.png)
> *`Network Diagram`*

### IP Addressing Scheme
The network is segmented into separate LAN, DMZ, management, and VPN networks across two sites. This structure improves security by isolating internal systems, public-facing services, and administrative devices while maintaining secure connectivity between locations.

* **WAN 1 / Internet Network:** `192.168.64.0/24`
* **Site 1 LAN:** `192.168.15.0/24`
* **Site 2 LAN:** `192.168.10.0/24`
* **Site 1 Apache Server (DMZ):** `192.168.15.20/24`
* **Site 2 SSH Server (DMZ):** `192.168.10.30/24`
* **Site 1 Management:** `192.168.15.10/24`
* **Site 2 Management:** `192.168.10.10/24`
* **Site-to-Site VPN (WAN 2):** `Site 1: 1.1.15.1, Site 2: 1.1.15.2`
* **Splunk Enterprise Server:** `206.206.206.12/24`
* **Kali Linux (Attacker):** `DHCP`
* **Remote Access User (Client VPN):** `192.168.15.X/24`
***

## VPN Implementation
This section includes the site-to-site VPN between the two sites and remote client VPN information. VPNs are critical for secure communication over untrusted networks, ensuring that traffic between branches and remote users is encrypted and authenticated.



### Site-to-Site VPN 
An IPsec tunnel was established between the two FortiGate firewalls to securely connect Site 1 and Site 2 over the public internet. This tunnel enables secure communication between branch offices while maintaining network segmentation.


* **Site 1 Tunnel Up**
![Site 1 Tunnel Up](images/Site1TunUP.png)

* **Site 1 Tunnel Configuration**
![Site 1 Tunnel Up](images/VPNTunnelSite1.png)

* **Site 2 Tunnel Up**
![Site 2 Tunnel Up](images/Site2TunnelUp.png)

* **Site 2 Tunnel Configuration**
![Site 2 Tunnel Config](images/Site2TunnelConfig.png)
---
### Client-Based VPN (Remote Access)
The remote client tunnel uses the L2TP protocol, configured in Windows Settings, to allow remote users to securely access internal resources.
* **Client Tunnel Up**
![Client Tunnel Up](images/Remote_Access_Tunnel.png)

* **Client Configuration**
![Client Config](images/Client_Tunnel.png)

* **Credentials for VPN User**
![VPN User](images/VPNuser.png)

* **Connection in Windows**
![VPN User](images/VPNconnected.png)

* **Connection Properties**
![Connection Propertie](images/Windows_VPN_Connection_Properties.png)

***

## Server Deployment
Internal services were hosted on servers to simulate real-world enterprise workloads. 

* **Apache Web Server**
  ![Apache Status](images/Apache2Status.png)
  ![Apache Server IP](images/Apacheserverip.png)
* **SSH Server**
  ![Site 2 SSH Status](images/SSHserverstatus.png)
  ![SSH IP](images/SSHserverip.png)
  
***
  
  ## VPN Connection to Servers
This section validates that remote VPN users can securely access internal servers. Successful connectivity confirms that routing, firewall policies, and segmentation are working as intended.
* **Pings**
  ![Pings](images/VPN_pinging_servers.png)
* **SSH**
  ![SSH](images/SSH_Connected.png)
* **Apache2 (Webserver)**
  ![apache](images/web_server_connected.png)

***

## Centralized Logging & Splunk
All network components (firewalls, servers, VPNs) forward logs to a centralized Splunk Enterprise instance for monitoring, correlation, and threat detection. Centralized logging is critical for identifying security events across the entire infrastructure from a single location.

### Syslog Configuration
* **Site 1 FortiGate A on UDP port 514**
  ![Fortigate Syslogs](images/Site1syslogsettings.png)

* **Site 2 FortiGate B on UDP port 514**
  ![Site 2 Syslog Fortigate](images/site2syslogfortigate.png)
  
* **Apache on UDP port 5515**
  ![Apache Syslog](images/Apachesyslog.png)
  
* **SSH on UDP port 5517**
  ![Apache Syslog](images/SSHserverconfig.png) 

***
  
### Splunk Logs

* **Site 1 Fortigate A Syslogs** 
![Fortigate Syslogs](images/FortigateAlogs.png)

* **Site 2 Fortigate B Syslogs** 
![Fortigate Syslogs](images/FortigateBlogs.png) 

* **Site 1+2 VPN Syslogs**
![Fortigate Syslogs](images/VPNlogs.png)

* **SSH Syslogs**
![Fortigate Syslogs](images/SSHlogs.png)

* **Apache2 Syslogs (Web Server)**
![Apache2 Syslogs](images/apache2logs.png)


***



## Attack Simulation & Detection
To validate the security and monitoring capabilities, controlled attacks were simulated using a Kali Linux VM. These simulations confirm that the firewall policies, intrusion prevention, and logging are functioning correctly.

### DoS Protection
Before attacks were executed, a DoS policy was configured to block unwanted traffic, instruct the firewall to drop malicious packets, and prevent the firewall from crashing under flood conditions.

![DOS Policy](images/dospolicy.png)
![DOS Protect 1](images/dosprotect1.png)
![DOS Protect 2](images/dosprotect2.png)



### Nmap Port Scan 
![NMAP Attack](images/NMAPAttack.png)
![NMAP Port Scan](images/Portscandash.png)
![NMAP Splunk](images/nmapattacksplunk.png) 

### ICMP Attack 
![ICMP Flood](images/ICMPFlood.png)
![ICMP in Fortigate](images/icmpfloodfortigate.png)
![ICMP Flood in Splunk](images/icmpfloodsplunk.png) 

### UDP Attack 
![UDP Flood](images/UDPFlood.png)
![UDP Flood in Fortigate](images/udpfloodfortigate.png)
![UDP Flood in Splunk](images/udpfloodsplunk.png) 

***
## Splunk Dashboard
The Splunk dashboard provides a centralized view of security events, allowing for rapid identification of suspicious activity and correlation across multiple data sources.
* [Splunk Dashboard PDF](images/Splunk%20Logs.pdf)

## Configuration Files
The following FortiGate configuration files are included as sanitized evidence of the implemented policies and routing.
### Fortigate Configuration Files Sanitized 
* [Fortigate A Site 1](images/FG-Group15-A_7-2_1762_202605261026.conf)
* [Fortigate B Site 2](images/FG-Group15-B_7-0_0667_202605261300.conf)

***

## Extra Items
These bonus tasks were completed beyond the required scope, demonstrating additional technical effort and automation.
### Fortigate Automation
* [Fortigate automation python](images/fortigate_automation.py)
* [Fortigate automation text file](images/Fortigate_automation.txt)

### Splunk Rules
* [Splunk rule file](images/fortigate_splunk_correlation_rules.spl)
* [Splunk rule text file](images/fortigate_splunk_correlation_rules.txt)

### Integration
* Integration with other project groups was out of scope for this implementation.    
