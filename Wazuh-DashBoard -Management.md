# Wazuh Dashboard Management Guide

### For SOC Monitoring & Log Analysis

**Wazuh 4.14.3 – All-in-One OVA Deployment**

---

## 📌 Overview

This document provides complete instructions for managing and using the Wazuh Dashboard after successful deployment of the Wazuh SIEM server. It focuses on:

* Agent management
* Log viewing in the Explore section
* Custom monitoring using `agent.id`, `agent.name`, and `rule.description`
* Best practices for SOC teams

---

## 🎯 Objectives

* Access and secure the Wazuh Dashboard
* Manage agents (add, remove, group, status check)
* Configure log monitoring in Explore section
* Create effective queries using `agent.id`, `agent.name`, and `rule.description`
* Build dashboards for SOC operations
* Verify integrations (Sysmon, VirusTotal, FIM)

---

## 🖥️ Dashboard Access

**URL:** `https://150.1.7.158`

### Default Credentials

* **Username:** `admin`
* **Password:** `admin`

### ⚠️ First-Time Actions (Mandatory)

* Change the default password immediately
* Enable SSL certificate (if not configured)
* Restrict access via firewall (allow only SOC IPs)

---

## 1. 🧑‍💻 Agents Management

Navigate to:
**Wazuh → Agents Management**

### Key Functions

* **Summary Tab:** View agent status (Active / Disconnected / Never connected)
* **Deploy New Agent:** Generate install commands (Windows/Linux)
* **Groups:**

  * Create groups (e.g., `production-endpoints`)
  * Apply shared configurations
* **Agent Details:**

  * Inventory (OS, hardware, packages)
  * Configuration
  * Logs
  * SCA results

### 🔧 Server Commands (Verification)

```bash
/var/ossec/bin/agent_control -l
/var/ossec/bin/manage_agents
```

---

## 2. 📊 Log Monitoring (Explore Section)

### Steps to Access

1. Log into Wazuh Dashboard
2. Click **Explore**
3. Select index pattern:

   * `wazuh-alerts-*` (Recommended)
   * `wazuh-*`

### 📌 Important Fields

| Field            | Description     | Example          |
| ---------------- | --------------- | ---------------- |
| agent.id         | Unique agent ID | 001              |
| agent.name       | Hostname        | WIN10-TEST       |
| rule.description | Alert message   | Malware detected |
| rule.level       | Severity (0–16) | >=12 critical    |
| data.source      | Log source      | sysmon           |
| @timestamp       | Event time      | Time filtering   |

---

## 🔎 Useful Queries (Copy-Paste Ready)

### 1. Filter by Agent

```text
agent.id: "001"
```

or

```text
agent.name: "WIN10-TEST"
```

### 2. Filter by Rule Description

```text
rule.description: "File added to downloads directory"
```

Partial match:

```text
rule.description: *downloads*
```

### 3. Agent + Rule (SOC Use Case)

```text
agent.name: "WIN10-TEST" AND rule.description: *Sysmon*
```

```text
agent.id: "001" AND rule.description: *malware*
```

### 4. High Severity Alerts

```text
rule.level: >= 12
```

### 5. Sysmon Logs

```text
data.win.eventdata.channel: "Microsoft-Windows-Sysmon/Operational"
```

### 6. VirusTotal Alerts

```text
rule.description: *VirusTotal*
```

### 7. File Integrity Monitoring (FIM)

```text
rule.description: *File* AND rule.description: *modified*
```

---

## 3. 📈 Creating a SOC Dashboard

### Steps

1. Go to **Dashboard**
2. Click **Create New Dashboard**

### Recommended Visualizations

* **Top Agents** → Data Table (`agent.name`)
* **Alerts by Rule Description** → Pie Chart
* **Alerts Over Time** → Line Chart (`@timestamp`)
* **Top Rule Levels** → Bar Chart (`rule.level`)
* **Recent Alerts** → Saved Search

💡 Tip: Save frequently used queries in Explore for reuse.

---

## 4. 🛠️ Verification & Troubleshooting

### Check Agent Status

* Dashboard → Agents Management → Summary
* Server:

```bash
/var/ossec/bin/agent_control -l
```

### Live Logs

```bash
tail -f /var/ossec/logs/ossec.log
```

### ⚠️ Common Issues & Fixes

| Issue                    | Solution                            |
| ------------------------ | ----------------------------------- |
| Agent Disconnected       | Check connectivity (port 1514/1515) |
| No logs in Explore       | Wait 1–2 minutes, refresh index     |
| Missing rule.description | Verify Sysmon `<localfile>` config  |

---

## ✅ Best Practices

* Use **agent groups** instead of individual configs
* Apply Sysmon, FIM, VirusTotal at group level
* Monitor **high severity alerts (>=12)** regularly
* Maintain saved searches for SOC workflows

---

## 📌 Notes

This guide is designed for SOC teams to efficiently monitor endpoints, investigate alerts, and maintain consistent configurations across environments using Wazuh.

