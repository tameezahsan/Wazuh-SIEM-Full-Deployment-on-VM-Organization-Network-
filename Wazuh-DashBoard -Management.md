# Wazuh Dashboard Management Guide
**SOC Monitoring & Log Analysis**  
**(Wazuh 4.14.4 – All-in-One OVA Deployment)**

📌 **Overview**  
This guide covers complete management and usage of the **Wazuh Dashboard** after deploying the Wazuh server. It focuses on agent management, powerful log searching in the **Explore** section using `agent.id`, `agent.name`, and `rule.description`, and building effective SOC workflows.

🎯 **Objectives**
- Secure access to Wazuh Dashboard
- Manage agents efficiently
- Master log monitoring using key fields (`agent.id`, `agent.name`, `rule.description`)
- Create useful queries and basic dashboards
- Verify Sysmon, FIM, and VirusTotal integrations

## Dashboard Access

**URL:** `https://150.1.7.158` (Replace with your Host-Only IP)  
**Default Login:**  
- Username: `admin`  
- Password: `admin`

**Security Steps (Do First):**
- Change default password immediately
- Restrict access to internal SOC IPs only
- Enable/verify SSL

---

### 1. Agents Management

Navigate to **Wazuh → Agents management**

**Main Features:**
- **Summary**: Overview of all agents (Active / Disconnected status)
- **Deploy new agent**: Recommended method to add Windows/Linux endpoints
- **Groups**: Create groups (e.g., `production-endpoints`) and apply unified configuration
- Click any agent for details: Inventory, Configuration, Logs, SCA results

**CLI Verification (on Wazuh Server):**
```bash
/var/ossec/bin/agent_control -l
