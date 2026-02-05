# SOC Lab – Wazuh & pfSense

## 📌 Overview
This repository documents the design and implementation of a **Security Operations Center (SOC) home lab**
using **Wazuh** as the SIEM/XDR platform and **pfSense** as the perimeter firewall.

The goal of this lab is to simulate real-world SOC workflows including:
- Log collection
- Alerting
- Incident response
- Detection engineering

## 🧱 Architecture
The lab consists of:
- Wazuh Manager
- Wazuh Agents (Linux & Windows)
- pfSense firewall
- Monitored endpoints

Detailed diagrams and topology are available in the `architecture/` directory.

## 📂 Repository Structure
- `architecture/` – Network topology and diagrams
- `wazuh/` – Manager, agents, rules, dashboards
- `pfsense/` – Firewall rules and integrations
- `playbooks/` – Incident response playbooks
- `logs/` – Sample logs and alerts
- `scripts/` – Deployment and maintenance scripts

## 🚀 Status
🛠️ Work in progress – actively building and documenting the lab.
