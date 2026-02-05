# SOC Lab Architecture Overview

## 🎯 Purpose
This project implements a **Security Operations Center (SOC) laboratory**
designed to simulate real-world detection, monitoring, and incident response
using open-source technologies.

The lab focuses on:
- Log collection and correlation
- Network visibility
- Endpoint monitoring
- Alert triage and analysis

## 🧱 Core Components
The SOC lab is composed of the following main components:

- **pfSense Firewall**
  - Network segmentation
  - Traffic filtering
  - Firewall and VPN logs

- **Wazuh Manager**
  - Central log analysis
  - Security rule engine
  - Agent management

- **Wazuh Agents**
  - Linux endpoints
  - Windows endpoints
  - File integrity monitoring
  - Syslog forwarding

- **SOC Analyst Workstation**
  - Alert review
  - Investigation
  - Playbook execution

## 🔄 Data Flow (High Level)
1. Network traffic passes through pfSense
2. Logs are generated and forwarded to Wazuh
3. Endpoints send security events via Wazuh agents
4. Alerts are analyzed by the SOC analyst

## 🧪 Lab Characteristics
- Fully isolated environment
- No production or real-world data
- Designed for learning and experimentation
