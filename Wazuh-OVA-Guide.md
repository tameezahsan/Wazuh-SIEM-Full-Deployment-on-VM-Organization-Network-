# 📥 Wazuh OVA Image Download Guide (VM Deployment)

## 📌 Overview

This document provides the official source and reference for downloading the **Wazuh Virtual Machine (OVA) image** used for enterprise SIEM deployment in virtualized environments.

The OVA image simplifies deployment by providing a pre-configured Wazuh environment including:

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

---

## 🔗 Official Download Link

The Wazuh OVA image can be accessed from the official documentation:

👉 [Wazuh Virtual Machine Deployment Guide](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html)

---

## 🖥️ Supported Virtualization Platforms

The OVA image can be deployed on:

* VMware Workstation / ESXi
* Oracle VirtualBox
* Any OVA-compatible hypervisor

---

## ⚙️ Recommended VM Configuration

* CPU: 4–8 cores
* RAM: 8–16 GB
* Storage: 100+ GB SSD
* Network: Dual Adapter (NAT + Host-Only recommended)

---

## 🔐 Purpose in SOC Environment

The Wazuh OVA image is used to quickly deploy a **Security Information and Event Management (SIEM) platform** for:

* Centralized log collection
* Endpoint security monitoring
* Threat detection and response
* SOC lab and enterprise deployment

---

## 🚀 Deployment Note

After importing the OVA image:

1. Configure network adapters (NAT + Host-only)
2. Assign static IP for internal SOC communication
3. Connect endpoint agents to Wazuh manager
4. Verify dashboard accessibility via HTTPS

---

## 📄 Reference

Official Wazuh Documentation:
[https://documentation.wazuh.com](https://documentation.wazuh.com)
