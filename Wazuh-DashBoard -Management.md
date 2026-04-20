Wazuh Dashboard Management Guide
For SOC Monitoring & Log Analysis
(Wazuh 4.14.3 – All-in-One OVA Deployment)
📌 Overview
This document provides complete instructions for managing and using the Wazuh Dashboard after successful deployment of the Wazuh SIEM server. It focuses on agent management, log viewing in the Explore section, custom monitoring using agent.id / agent.name, and rule.description, along with best practices for SOC teams.
🎯 Objectives

Access and secure the Wazuh Dashboard
Manage agents (add, remove, group, status check)
Configure log monitoring in Explore section
Create effective queries using agent.id, agent.name, and rule.description
Build basic dashboards for daily SOC operations
Verify integrations (Sysmon, VirusTotal, FIM)

🖥️ Dashboard Access
URL: https://150.1.7.158 (Use your Wazuh VM Host-Only IP)
Default Credentials:

Username: admin
Password: admin

First-Time Actions (Mandatory):

Change the default password immediately after first login
Enable SSL certificate (if not already configured)
Set up proper firewall rules to allow only internal SOC IPs


1. Agents Management
Go to Wazuh → Agents management (left sidebar)
Key Functions:

Summary Tab: View all registered agents with status (Active / Disconnected / Never connected)
Deploy new agent: Easiest way to generate installation commands for Windows/Linux
Groups: Create and manage agent groups (Recommended: Create a group called production-endpoints and apply common config)
Agent details: Click on any agent to see:
Inventory (hardware, OS, packages)
Configuration
Logs
SCA (Security Configuration Assessment) results


Useful Commands on Server (for cross-check):
Bash/var/ossec/bin/agent_control -l
/var/ossec/bin/manage_agents

2. Log Monitoring in Explore Section
This is the main area for viewing and searching logs/alerts.
Steps to Access:

Log into Wazuh Dashboard
From left menu, click Explore
Select index pattern: wazuh-alerts-* (recommended) or wazuh-*

Recommended Fields for SOC Monitoring:

FieldDescriptionUsage Exampleagent.idAgent unique ID001, 002agent.nameHostname of the endpointWIN10-TEST, LINUX-SERVERrule.descriptionRule name / alert messageMost important for filteringrule.levelAlert severity (0-16)>= 12 for criticaldata.sourceLog source (sysmon, syscheck, etc.)Microsoft-Windows-Sysmon/OperationaltimestampEvent timeTime-based filtering
Powerful Queries for Explore Section (Copy-Paste Ready)
1. View logs from a specific agent:
textagent.id: "001"
or
textagent.name: "WIN10-TEST"
2. Filter by rule description (best for meaningful alerts):
textrule.description: "File added to downloads directory"
or partial match:
textrule.description: *downloads*
3. Combine agent + rule description (Most Used in SOC):
textagent.name: "WIN10-TEST" AND rule.description: *Sysmon*
textagent.id: "001" AND rule.description: *malware*
4. High severity alerts only:
textrule.level: >= 12
5. Sysmon specific events:
textdata.win.eventdata.channel: "Microsoft-Windows-Sysmon/Operational"
6. VirusTotal enriched alerts:
textrule.description: *VirusTotal*
7. File Integrity Monitoring (FIM) alerts:
textrule.description: *File* AND rule.description: *modified*

3. Creating a Basic SOC Monitoring Dashboard

Go to Dashboard (left menu)
Click Create new dashboard
Add the following visualizations:
Top Agents → Visualization type: Data Table (using agent.name)
Alerts by Rule Description → Pie chart or Data Table
Alerts Over Time → Line chart (using timestamp)
Top Rule Levels → Horizontal bar (rule.level)
Recent Alerts → Saved Search from Explore


Tip: Save your most used queries in Explore as Saved Searches for quick reuse.

4. Verification & Troubleshooting
Check Agent Status:

In Dashboard → Agents management → Summary
On server: /var/ossec/bin/agent_control -l

Live Log Monitoring:
Bashtail -f /var/ossec/logs/ossec.log
Common Issues & Fixes:

Agent shows Disconnected → Check network reachability to 150.1.7.158 on port 1514/1515
No logs in Explore → Wait 1-2 minutes after agent registration, then refresh index
Rule description not showing → Make sure agent configuration has proper <localfile> for Sysmon

Best Practice:

Always use agent groups instead of editing individual agent config files
Apply Sysmon, FIM, and VirusTotal settings at group level for consistency
