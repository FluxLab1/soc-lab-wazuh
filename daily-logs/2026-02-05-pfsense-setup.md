# SOC Lab Daily Log - 2026-02-05 (Wednesday)

## 🎯 Today's Focus
- Created pfSense documentation (setup, firewall rules, syslog integration)
- Configured pfSense VM networking with bridged + internal adapters
- Successfully accessed pfSense web GUI via SSH tunnel
- Prepared for Wazuh syslog integration

## 🏗️ Infrastructure Changes
- pfSense NIC1: Changed from Internal to Bridged (enp0s31f6)
- pfSense LAN: Configured as 192.168.10.1/24 with DHCP (100-200)
- Ubuntu Server: Obtained 192.168.10.100 from pfSense DHCP
- SSH Tunnel: localhost:8080 → 192.168.10.1:443

## 📝 Documentation Created
- pfSense Setup Guide
- Firewall Rules Documentation
- Syslog Integration Guide

## ✅ Completed Tasks
- [x] pfSense VM network configuration
- [x] pfSense console setup (interfaces, DHCP)
- [x] Web GUI access via SSH tunnel
- [x] Changed default admin password

## 🔄 Next Steps
- [ ] Configure syslog forwarding to Wazuh
- [ ] Implement firewall rules
- [ ] Test log collection

---
**Time Spent:** ~2 hours
**Status:** pfSense operational
