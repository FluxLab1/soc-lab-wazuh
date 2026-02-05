# pfSense Firewall Rules - SOC Lab

## Network Zones
| Zone | Interface | Network | Purpose |
|------|-----------|---------|---------|
| WAN | em0 | External | Simulated threats |
| LAN | em1 | 192.168.10.0/24 | SOC infrastructure |
| DMZ | em2 | 192.168.20.0/24 | Monitored services |

## Rule Principles
- Default deny all
- Explicit allow only what's needed
- Log everything for Wazuh

## WAN Rules
| Action | Protocol | Source | Dest | Port | Log | Description |
|--------|----------|--------|------|------|-----|-------------|
| Block | Any | Any | RFC1918 | Any | Yes | Block spoofed IPs |
| Block | TCP/UDP | Any | Any | 135-139,445 | Yes | Block SMB |
| Block | Any | Any | Any | Any | Yes | Default deny |

## LAN Rules
| Action | Protocol | Source | Dest | Port | Log | Description |
|--------|----------|--------|------|------|-----|-------------|
| Pass | TCP | LAN net | Any | 80,443 | No | HTTP/HTTPS |
| Pass | UDP | LAN net | Any | 53 | No | DNS |
| Pass | TCP | LAN net | Any | 22 | Yes | SSH (logged) |
| Pass | TCP/UDP | LAN net | DMZ | 1514,1515 | Yes | Wazuh agents |
| Block | Any | LAN net | Any | Any | Yes | Default deny |

## Logging for Wazuh
```
Status → System Logs → Settings
  Remote Logging: Enable
  Server: 192.168.10.10:514
  Everything: ✓
```

## Related Docs
- [Setup Guide](pfsense-setup.md)
- [Syslog Integration](syslog-integration.md)

## Last Updated
- Date: 2026-02-05
- Status: Draft
