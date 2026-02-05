# Network Topology

## 🌐 Network Segments

### 1. WAN (Simulated Internet)
- External attacker simulation
- Update repositories
- Threat emulation tools

### 2. Firewall (pfSense)
- Acts as the network gateway
- Enforces security policies
- Generates firewall and VPN logs

### 3. Internal LAN
- SOC Infrastructure
- Endpoints under monitoring

## 🖥️ Internal Hosts

| Host | Role |
|-----|------|
| Wazuh Manager | Log correlation and alerting |
| Linux Endpoint | Server monitoring |
| Windows Endpoint | Workstation monitoring |
| SOC Analyst VM | Investigation and response |

## 🔁 Log & Alert Flow
- pfSense → Syslog → Wazuh Manager
- Endpoints → Wazuh Agent → Wazuh Manager
- Alerts → SOC Analyst

## 🔒 Security Controls
- Network segmentation
- Least privilege
- Centralized logging
- Integrity monitoring
