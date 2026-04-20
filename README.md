# Wazuh SIEM Full Deployment on VM (Organization Network)

## 📌 Overview

This repository provides a complete, production-ready guide for deploying a **Wazuh SIEM platform on a Virtual Machine (VM) using the official Wazuh 4.14.3 OVA image** inside an organization network.

It includes VM network configuration, endpoint integration, Sysmon telemetry, and VirusTotal threat intelligence enrichment.

This setup is designed for enterprise SOC environments requiring centralized monitoring, endpoint visibility, and secure internal network segmentation.

---

## 🎯 Objectives

* Deploy Wazuh SIEM on a VM using official OVA image
* Configure secure dual-network VM (NAT + Host-only)
* Integrate Windows and Linux endpoint agents
* Enable Sysmon-based deep endpoint monitoring
* Integrate VirusTotal for threat intelligence enrichment
* Maintain secure internal SOC communication

---

## 🏗️ System Architecture

```
Endpoints (Windows/Linux)
        │
        ▼
   Wazuh Agent
        │
        ▼
 Wazuh VM Server (OVA: Manager + Indexer + Dashboard)
        │
        ├── Log Analysis Engine
        ├── Sysmon Event Processing
        └── VirusTotal Integration
        │
        ▼
   Wazuh Dashboard (SOC Monitoring)
```

---

## 🖥️ OVA DEPLOYMENT (WAZUH SERVER)

The Wazuh server is deployed using the **official Wazuh 4.14.3 OVA image**, which comes preconfigured with all required components.

### Deployment Steps

1. Import the Wazuh 4.14.3 OVA image into VMware/VirtualBox
2. Allocate resources:

   * CPU: 4–8 cores
   * RAM: 8–16 GB
   * Storage: 100+ GB
3. Configure network adapters:

   * Adapter 1 → NAT (Internet access)
   * Adapter 2 → Host-Only (VMnet1 internal SOC network)
4. Start the VM

---

## 🌐 VM NETWORK CONFIGURATION

The VM uses a dual-network design:

| Adapter   | Type               | Purpose                             |
| --------- | ------------------ | ----------------------------------- |
| Adapter 1 | NAT                | Internet access (updates, packages) |
| Adapter 2 | Host-Only (VMnet1) | Internal SOC network                |

---

### Network Setup Steps

```bash
sudo su
```

Configure NAT interface:

```bash
nano /etc/systemd/network/20-eth0.network
```

```
[Match]
Name=eth0

[Network]
DHCP=yes
```

Configure internal interface:

```bash
nano /etc/systemd/network/30-eth1.network
```

```
[Match]
Name=eth1

[Network]
Address=150.1.7.158/24
```

Apply configuration:

```bash
ip addr flush dev eth1
systemctl restart systemd-networkd
```

Verify:

```bash
networkctl list
ip a
```

---

## 💻 ENDPOINT DEPLOYMENT

### Windows Agent

```powershell
Sysmon.exe -accepteula -i .\sysmonconfig-export.xml
Sysmon64.exe -c
```

### Linux Agent

```bash
sudo apt install wazuh-agent
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

---

## 🔑 AGENT REGISTRATION

On Wazuh VM:

```bash
/var/ossec/bin/manage_agents
```

* Add agent
* Generate key
* Copy key to endpoint

---

## 🧠 SYSMON INTEGRATION

Install entity ["software","Sysmon","Microsoft Sysinternals system monitoring tool"]

Enable logging:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Provides:

* Process monitoring
* Network tracking
* File and registry changes

---

## 🌍 VIRUSTOTAL INTEGRATION

Integrate entity ["organization","VirusTotal","malware analysis and threat intelligence platform"]

Used for file hash enrichment and malware detection.
```xml
<integration>
    <name>virustotal</name>
    <api_key>YOUR_API_KEY</api_key>
    <group>syscheck</group>
    <alert_format>json</alert_format>
  </integration>
```

---

## 📁 FILE INTEGRITY MONITORING (FIM)
This will monitor files at download location.
```xml
<syscheck>
  <directories check_all="yes" realtime="yes" report_changes="yes" recursion_level="0">C:\Windows</directories>
  <directories check_all="yes" realtime="yes" report_changes="yes">C:\Users\*\Downloads</directories>
</syscheck>
```

---

## 📊 VERIFICATION
All above intregration is done in agent configuration file to run properly on end-devices after deploying.

```bash
/var/ossec/bin/agent_control -l
tail -f /var/ossec/logs/ossec.log
```

---

## 🔐 SECURITY BEST PRACTICES

* Use static IP for VM (eth1)
* Keep SOC network isolated
* Restrict dashboard access
* Secure agent keys
* Enable TLS communication
* Monitor VM resources

---

## 🚀 FINAL OUTCOME

This deployment provides a complete SOC monitoring solution:

* Centralized SIEM platform
* Real-time endpoint visibility
* Sysmon deep telemetry
* VirusTotal threat intelligence enrichment
* Secure organization network architecture

---

## 📄 LICENSE

MIT License

---

## 📬 CONTACT

Open an issue in this repository for support.

---
