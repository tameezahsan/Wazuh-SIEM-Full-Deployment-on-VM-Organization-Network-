# Wazuh SIEM Full Deployment on VM (Organization Network)

## 📌 Overview

This repository provides a complete guide for deploying a **Wazuh Security Information and Event Management (SIEM) platform on a Virtual Machine (VM)** inside an organization network. It includes server installation, endpoint integration, Sysmon configuration, and VirusTotal threat intelligence integration.

This setup is designed for enterprise SOC environments where centralized monitoring and endpoint visibility are required.

---

## 🎯 Objectives

* Deploy Wazuh SIEM on a dedicated VM
* Centralize security monitoring for organization endpoints
* Integrate Windows and Linux agents
* Enable Sysmon-based deep endpoint visibility
* Integrate VirusTotal for threat intelligence enrichment
* Ensure secure internal network communication

---

## 🏗️ System Architecture

```
Endpoints (Windows/Linux)
        │
        ▼
   Wazuh Agent
        │
        ▼
 Wazuh VM Server (Manager + Indexer + Dashboard)
        │
        ├── Log Analysis Engine
        ├── Sysmon Event Processing
        └── VirusTotal Integration
        │
        ▼
   Wazuh Dashboard (SOC Monitoring)
```

---

## 🖥️ VM Requirements

### Recommended Specifications

* CPU: 4–8 vCPU
* RAM: 8–16 GB (32 GB recommended for production)
* Storage: 100–500 GB SSD
* OS: Ubuntu 22.04 LTS
* Static IP: Required (e.g., 192.168.1.10)

---

## 🌐 Network Configuration

### Required Ports

| Port  | Protocol | Purpose             |
| ----- | -------- | ------------------- |
| 1514  | TCP      | Agent communication |
| 1515  | TCP      | Agent enrollment    |
| 55000 | TCP      | Wazuh API           |
| 443   | HTTPS    | Dashboard           |

### Firewall Rule Example

```bash
sudo ufw allow 1514/tcp
sudo ufw allow 1515/tcp
sudo ufw allow 55000/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

---

## ⚙️ Wazuh Server Installation (VM)

### Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2: Download Installer

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
chmod +x wazuh-install.sh
```

### Step 3: Install Full Stack

```bash
sudo ./wazuh-install.sh -a
```

This installs:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

---

## 🌍 Access Wazuh Dashboard

Open browser:

```
https://<VM_IP>
```

Login credentials are generated during installation.

---

## 🔐 Wazuh Server Configuration

Edit configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

### Allow Organization Network

```xml
<remote>
  <connection>secure</connection>
  <port>1514</port>
  <protocol>tcp</protocol>
  <allowed-ips>192.168.0.0/16</allowed-ips>
</remote>
```

---

## 💻 Endpoint Deployment

### 🪟 Windows Agent

```powershell
msiexec /i wazuh-agent.msi /q WAZUH_MANAGER="192.168.1.10"
Start-Service wazuh
```

### 🐧 Linux Agent

```bash
sudo apt install wazuh-agent
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

---

## 🔑 Agent Registration (Static Mode)

On Wazuh VM:

```bash
/var/ossec/bin/manage_agents
```

Steps:

1. Add agent
2. Generate key
3. Copy key

On endpoint:

* Paste key into agent manager

---

## 🧠 Sysmon Integration (Windows)

Install entity["software","Sysmon","Microsoft Sysinternals system monitoring tool"]

### Install Sysmon

```powershell
sysmon64.exe -i sysmonconfig.xml
```

### Enable in Wazuh Agent

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Sysmon provides:

* Process monitoring
* Network connection tracking
* Registry and file changes

---

## 🌍 VirusTotal Integration

Integrate entity["organization","VirusTotal","malware analysis and threat intelligence platform"]

### Configuration

```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_API_KEY</api_key>
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

Features:

* File hash analysis
* Malware detection enrichment
* Threat intelligence correlation

---

## 📁 File Integrity Monitoring (FIM)

```xml
<syscheck>
  <directories check_all="yes">/home</directories>
  <frequency>3600</frequency>
</syscheck>
```

---

## 📊 Verification

### Check Active Agents

```bash
/var/ossec/bin/agent_control -l
```

### Check Logs

```bash
tail -f /var/ossec/logs/ossec.log
```

### Dashboard Checks

* Active agents connected
* Security alerts generating
* Sysmon logs visible
* VirusTotal alerts working

---

## 🔐 Security Best Practices

* Use static IP for VM
* Restrict dashboard access to IT team
* Enable TLS encryption for agents
* Regularly update Wazuh and Sysmon
* Secure API keys
* Monitor VM resource usage

---

## 🚀 Final Outcome

After deployment, the organization achieves:

* Centralized SIEM monitoring system
* Real-time endpoint visibility
* Advanced Windows telemetry (Sysmon)
* Threat intelligence enrichment (VirusTotal)
* Secure and scalable SOC architecture inside VM infrastructure

---

## 📄 License

MIT License

---

## 📬 Contact

For support, open an issue in this repository.
