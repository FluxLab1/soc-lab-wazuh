# pfSense Setup Guide - VirtualBox Installation

## 🎯 Purpose
This guide documents the installation and initial configuration of pfSense as the network firewall for the SOC lab environment.

pfSense will serve as:
- Network gateway and router
- Firewall for traffic filtering
- Log generator for security monitoring
- VPN endpoint (future)

---

## 📋 Prerequisites

### Downloads
- [ ] pfSense CE ISO: https://www.pfsense.org/download/
  - Version: (e.g., 2.7.2)
  - Architecture: AMD64
- [ ] VirtualBox installed and running

### Network Planning
Before installation, plan your network segments:

| Network Segment | Interface | IP Range | Purpose |
|----------------|-----------|----------|---------|
| WAN (Internet) | em0 | DHCP/NAT | Simulated external |
| LAN (Internal) | em1 | 192.168.10.0/24 | SOC infrastructure |
| DMZ (Optional) | em2 | 192.168.20.0/24 | Monitored services |

---

## 🖥️ VirtualBox VM Creation

### Step 1: Create New VM
```
Name: pfSense-Firewall
Type: BSD
Version: FreeBSD (64-bit)
Memory: 2048 MB (minimum 1024 MB)
Hard Disk: 20 GB (VDI, dynamically allocated)
```

### Step 2: Configure Network Adapters
```
Adapter 1 (WAN):
  - Attached to: NAT or Bridged
  - Promiscuous Mode: Allow All
  
Adapter 2 (LAN):
  - Attached to: Internal Network
  - Name: SOC-LAN
  - Promiscuous Mode: Allow All
```

---

## 🚀 pfSense Installation

### Boot and Install
1. Start the VM
2. Accept copyright notice (Enter)
3. Select **Install** (not Live Mode)
4. Choose keymap: US
5. Partitioning: **Auto (ZFS)** recommended
6. Select virtual disk (ada0)
7. Reboot when complete

---

## ⚙️ Initial Configuration

### Console Setup
```
Should VLANs be set up now? n

WAN interface: em0
LAN interface: em1

Configure IPv4 via DHCP? n
IPv4 address: 192.168.10.1
Subnet mask: 24

Enable DHCP server on LAN? y
Start address: 192.168.10.100
End address: 192.168.10.200
```

---

## 🌐 Web GUI Access
1. Connect a VM to SOC-LAN network
2. Navigate to: https://192.168.10.1
3. Default credentials:
   - Username: admin
   - Password: pfsense
4. **Change password immediately**

---

## 🔗 Related Documentation
- [Firewall Rules](firewall-rules.md)
- [Syslog Integration](syslog-integration.md)

---

## 📅 Last Updated
- Date: 2026-02-05
- Status: Draft
