# SOC Lab Setup Guide

## Overview
This guide documents the setup of a Security Operations Center (SOC) lab environment using Wazuh for security monitoring and threat detection.

## Architecture

### Components
- **Host Machine**: Mini PC running Docker
  - IP Address: `192.168.0.10`
  - OS: Linux with Docker installed
  - Wazuh Manager, Indexer, and Dashboard (containerized)

- **Monitored System**: Ubuntu Server 24.04.3 LTS
  - Hostname: `lab-server-01`
  - Platform: VirtualBox VM (Oracle)
  - IP Address: `10.0.2.15` (internal), accessible via SSH on port 2222
  - Wazuh Agent installed

---

## Prerequisites

- Docker and Docker Compose installed
- Git installed
- Minimum 4GB RAM available
- VirtualBox (or similar virtualization platform)
- Ubuntu Server VM configured

---

## Installation Steps

### 1. Prepare the Host System

Set the required kernel parameter for Elasticsearch/OpenSearch:

\`\`\`bash
sudo sysctl -w vm.max_map_count=262144
\`\`\`

To make this permanent, add it to \`/etc/sysctl.conf\`:
\`\`\`bash
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
\`\`\`

### 2. Clone Wazuh Docker Repository

Create a working directory and clone the official Wazuh Docker repository:

\`\`\`bash
mkdir -p ~/soc-lab && cd ~/soc-lab
git clone https://github.com/wazuh/wazuh-docker.git -b v4.10.2 --single-branch
cd wazuh-docker/single-node
\`\`\`

### 3. Generate TLS Certificates

Generate the required certificates for secure communication between Wazuh components:

\`\`\`bash
sudo docker compose -f generate-indexer-certs.yml run --rm generator
\`\`\`

Expected output:
\`\`\`
05/02/2026 04:00:13 INFO: Generating the root certificate.
05/02/2026 04:00:13 INFO: Generating Admin certificates.
05/02/2026 04:00:14 INFO: Admin certificates created.
05/02/2026 04:00:14 INFO: Generating Wazuh indexer certificates.
05/02/2026 04:00:14 INFO: Wazuh indexer certificates created.
05/02/2026 04:00:14 INFO: Generating Filebeat certificates.
05/02/2026 04:00:14 INFO: Wazuh Filebeat certificates created.
05/02/2026 04:00:14 INFO: Generating Wazuh dashboard certificates.
05/02/2026 04:00:14 INFO: Wazuh dashboard certificates created.
\`\`\`

### 4. Deploy Wazuh Stack

Start all Wazuh services using Docker Compose:

\`\`\`bash
sudo docker compose up -d
\`\`\`

This will pull and start three main containers:
- \`single-node-wazuh.manager-1\` - Wazuh Manager
- \`single-node-wazuh.indexer-1\` - Wazuh Indexer (OpenSearch)
- \`single-node-wazuh.dashboard-1\` - Wazuh Dashboard

### 5. Verify Deployment

Check that all containers are running:

\`\`\`bash
sudo docker ps
\`\`\`

Check indexer health status:

\`\`\`bash
sudo docker logs single-node-wazuh.indexer-1 | grep "status"
\`\`\`

Look for: \`Cluster health status changed from [YELLOW] to [GREEN]\`

---

## Agent Installation (Ubuntu Server)

### 1. SSH into Your Ubuntu VM

\`\`\`bash
ssh -p 2222 analyst@127.0.0.1
\`\`\`

### 2. Download and Install Wazuh Agent

\`\`\`bash
curl -so wazuh-agent-4.10.2.deb https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.10.2-1_amd64.deb

sudo WAZUH_MANAGER='192.168.0.10' WAZUH_AGENT_NAME='lab-server-01' dpkg -i wazuh-agent-4.10.2.deb
\`\`\`

### 3. Enable and Start Wazuh Agent

\`\`\`bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
\`\`\`

### 4. Configure Firewall (if applicable)

Allow Wazuh communication ports:

\`\`\`bash
sudo ufw allow 1514/tcp  # Agent communication
sudo ufw allow 1515/tcp  # Agent enrollment
\`\`\`

### 5. Test Connectivity

Verify the agent can reach the manager:

\`\`\`bash
nc -zv 192.168.0.10 1514
\`\`\`

Expected output: \`Connection to 192.168.0.10 1514 port [tcp/*] succeeded!\`

### 6. Restart Agent

\`\`\`bash
sudo systemctl restart wazuh-agent
\`\`\`

---

## Verification

### Check Connected Agents

From the host machine, list all connected agents:

\`\`\`bash
sudo docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/agent_control -l
\`\`\`

Expected output:
\`\`\`
Wazuh agent_control. List of available agents:
   ID: 000, Name: wazuh.manager (server), IP: 127.0.0.1, Active/Local
   ID: 001, Name: lab-server-01, IP: any, Active
\`\`\`

### Access Wazuh Dashboard

1. Open browser and navigate to: \`https://192.168.0.10\`
2. Login with default credentials:
   - **Username**: \`admin\`
   - **Password**: \`SecretPassword\`

3. Navigate to **Server Management** → **Endpoints Summary**
4. Verify \`lab-server-01\` appears with "Active" status

---

## Testing Security Detection

### Generate Test Alerts

SSH into your Ubuntu VM and simulate failed login attempts:

\`\`\`bash
ssh usuario_falso@localhost
\`\`\`

Enter incorrect passwords 2-3 times to generate failed authentication alerts.

### View Alerts in Dashboard

1. Navigate to **Threat Hunting** → **Events**
2. Filter by agent: \`lab-server-01\`
3. Look for Rule ID \`5710\` (SSH authentication failed)
4. Review alert details, timestamps, and source IPs

---

## Default Credentials

### Wazuh Dashboard
- **URL**: \`https://192.168.0.10\`
- **Username**: \`admin\`
- **Password**: \`SecretPassword\`

### Internal Components
- **Indexer Password**: Found via \`sudo docker exec single-node-wazuh.manager-1 grep "password" /etc/filebeat/filebeat.yml\`

---

## Useful Commands

### Container Management

\`\`\`bash
# View all containers
sudo docker ps

# View container logs
sudo docker logs single-node-wazuh.manager-1
sudo docker logs single-node-wazuh.indexer-1
sudo docker logs single-node-wazuh.dashboard-1

# Restart a specific service
sudo docker compose restart wazuh.dashboard
sudo docker compose restart wazuh.manager

# Stop all services
sudo docker compose down

# Start all services
sudo docker compose up -d
\`\`\`

### Agent Management

\`\`\`bash
# List all agents
sudo docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/agent_control -l

# Get detailed agent info
sudo docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/agent_control -i 001

# View agent logs
sudo docker exec -it single-node-wazuh.manager-1 tail -f /var/ossec/logs/ossec.log
\`\`\`

### On the Agent (Ubuntu VM)

\`\`\`bash
# Check agent status
sudo systemctl status wazuh-agent

# View agent logs
sudo tail -f /var/ossec/logs/ossec.log

# Restart agent
sudo systemctl restart wazuh-agent

# Check connectivity to manager
nc -zv 192.168.0.10 1514
\`\`\`

---

## Troubleshooting

### Agent Not Showing as Active

1. Check agent service status:
   \`\`\`bash
   sudo systemctl status wazuh-agent
   \`\`\`

2. Verify manager IP in agent config:
   \`\`\`bash
   sudo cat /var/ossec/etc/ossec.conf | grep address
   \`\`\`

3. Check firewall rules on both host and VM

4. Restart agent:
   \`\`\`bash
   sudo systemctl restart wazuh-agent
   \`\`\`

### Dashboard Not Accessible

1. Verify containers are running:
   \`\`\`bash
   sudo docker ps
   \`\`\`

2. Check dashboard logs:
   \`\`\`bash
   sudo docker logs single-node-wazuh.dashboard-1
   \`\`\`

3. Restart dashboard:
   \`\`\`bash
   sudo docker compose restart wazuh.dashboard
   \`\`\`

### Permission Denied Errors

If you get Docker permission errors, add your user to the docker group:
\`\`\`bash
sudo usermod -aG docker $USER
newgrp docker
\`\`\`

---

## Network Topology

\`\`\`
┌─────────────────────────────────────────┐
│         Host Machine (192.168.0.10)     │
│                                         │
│  ┌───────────────────────────────────┐ │
│  │   Docker Containers               │ │
│  │                                   │ │
│  │  • Wazuh Manager (Port 1514/1515)│ │
│  │  • Wazuh Indexer (Port 9200)     │ │
│  │  • Wazuh Dashboard (Port 443)    │ │
│  └───────────────────────────────────┘ │
└─────────────────────────────────────────┘
                    │
                    │ Agent Communication
                    │ (Ports 1514/1515)
                    ▼
┌─────────────────────────────────────────┐
│   VirtualBox VM (lab-server-01)        │
│   Ubuntu Server 24.04.3 LTS            │
│   IP: 10.0.2.15                        │
│   SSH Port: 2222                       │
│                                         │
│   • Wazuh Agent 4.10.2                 │
│   • systemd-detect-virt: oracle        │
└─────────────────────────────────────────┘
\`\`\`

---

## Security Considerations

- **Change default passwords** immediately in production
- **Enable TLS/SSL** for all communications (already configured via certificates)
- **Implement firewall rules** to restrict access to management interfaces
- **Regular updates** of Wazuh components and agents
- **Monitor disk space** for log retention and indexer storage
- **Backup configurations** and detection rules regularly

---

## Next Steps

1. **Configure Custom Rules**: Create detection rules for specific threats
2. **Enable File Integrity Monitoring**: Monitor critical system files
3. **Configure Active Response**: Automate threat response actions
4. **Integrate Threat Intelligence**: Add VirusTotal, AlienVault OTX feeds
5. **Set Up Vulnerability Detection**: Enable vulnerability scanning
6. **Create Custom Dashboards**: Build visualizations for your environment
7. **Configure Email Alerts**: Set up notifications for critical events
8. **Add More Agents**: Expand monitoring to additional systems

---

## Resources

- [Official Wazuh Documentation](https://documentation.wazuh.com/)
- [Wazuh Docker GitHub](https://github.com/wazuh/wazuh-docker)
- [Wazuh Ruleset](https://github.com/wazuh/wazuh-ruleset)
- [Community Support](https://groups.google.com/g/wazuh)

---

## Version Information

- **Wazuh Version**: 4.10.2
- **Ubuntu Server**: 24.04.3 LTS
- **Kernel**: 6.8.0-94-generic
- **Docker Compose**: v2.x
- **Setup Date**: February 5, 2026

---

## License

This documentation is provided as-is for educational and lab purposes.
