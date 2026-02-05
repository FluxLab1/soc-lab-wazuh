# pfSense Syslog Integration with Wazuh

## 🎯 Purpose
Configure pfSense to forward firewall logs to Wazuh for centralized monitoring, correlation, and alerting.

---

## 📋 Prerequisites
- pfSense installed and accessible
- Wazuh Manager running at 192.168.10.10
- Network connectivity between pfSense and Wazuh

---

## ⚙️ pfSense Configuration

### Step 1: Enable Remote Logging
```
Status → System Logs → Settings

Remote Logging Options:
  ☑ Enable Remote Logging
  
Source Address: (leave default)

Remote Log Servers:
  Server 1: 192.168.10.10
  Port: 514
  Protocol: UDP (default) or TCP
  
Log Message Format: syslog (RFC 5424)

Everything: ☑ (sends all logs)
  Or select specific:
  ☑ Firewall Events
  ☑ System Events  
  ☑ DHCP Events
```

Click **Save**

---

## 🔧 Wazuh Manager Configuration

### Step 2: Configure Wazuh to Receive Syslog

Edit ossec.conf on Wazuh Manager:
```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add remote syslog section:
```xml
<remote>
  <connection>syslog</connection>
  <port>514</port>
  <protocol>udp</protocol>
  <allowed-ips>192.168.10.1</allowed-ips>
</remote>
```

Restart Wazuh:
```bash
sudo systemctl restart wazuh-manager
```

---

## 📊 Verify Log Reception

### On pfSense
Generate test traffic:
```
Diagnostics → Command Prompt
Execute: logger -p local0.info "Test syslog from pfSense"
```

### On Wazuh Manager
Check logs:
```bash
tail -f /var/ossec/logs/archives/archives.log | grep pfsense
tail -f /var/ossec/logs/alerts/alerts.log
```

Should see pfSense entries arriving.

---

## 🛡️ Wazuh Rules for pfSense

### Create Custom Rules
```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

Add pfSense-specific rules:
```xml
<group name="pfsense,firewall,">
  
  <!-- Blocked connection attempts -->
  <rule id="100010" level="5">
    <if_sid>5710</if_sid>
    <match>filterlog</match>
    <regex>block</regex>
    <description>pfSense: Connection blocked by firewall</description>
    <group>firewall_block,</group>
  </rule>

  <!-- Port scan detection -->
  <rule id="100011" level="10" frequency="10" timeframe="60">
    <if_matched_sid>100010</if_matched_sid>
    <same_source_ip />
    <description>pfSense: Possible port scan detected</description>
    <group>attack,recon,</group>
  </rule>

  <!-- Admin login success -->
  <rule id="100020" level="3">
    <match>webConfigurator authentication for user</match>
    <regex>webConfigurator authentication for user \S+ from</regex>
    <description>pfSense: Successful admin login</description>
  </rule>

  <!-- Admin login failure -->
  <rule id="100021" level="8">
    <match>webConfigurator authentication error for</match>
    <description>pfSense: Failed admin login attempt</description>
    <group>authentication_failed,</group>
  </rule>

</group>
```

Restart Wazuh:
```bash
sudo systemctl restart wazuh-manager
```

---

## 🧪 Testing Detection Rules

### Test 1: Blocked Traffic
From external VM, attempt blocked connection:
```bash
telnet 192.168.10.10 445
```

Check Wazuh alert:
```bash
tail -f /var/ossec/logs/alerts/alerts.log | grep "Connection blocked"
```

### Test 2: Port Scan
Run nmap from WAN:
```bash
nmap -sS 192.168.10.1
```

Wazuh should trigger port scan alert (rule 100011)

### Test 3: Failed Admin Login
Go to pfSense WebGUI, enter wrong password 3 times.

Check alerts for rule 100021.

---

## 📈 Wazuh Dashboard

### Create pfSense Dashboard
1. Login to Wazuh web interface
2. Navigate to Dashboard
3. Create custom dashboard for pfSense
4. Add visualizations:
   - Top blocked IPs
   - Blocked connections over time
   - Top blocked ports
   - Admin login attempts
   - Geographic map of blocked IPs

---

## 🚨 Alert Actions (Future)

### Email Alerts
Configure email for critical events:
```xml
<email_alerts>
  <email_to>soc@lab.local</email_to>
  <level>10</level>
  <group>attack,</group>
</email_alerts>
```

### Active Response
Auto-block aggressive scanners:
```xml
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>100011</rules_id>
</active-response>
```

---

## 🔍 Log Analysis Examples

### Find Top Blocked IPs
```bash
grep "Connection blocked" /var/ossec/logs/alerts/alerts.log | \
  grep -oP 'SRC=\K[0-9.]+' | sort | uniq -c | sort -rn | head -10
```

### Count Blocks by Port
```bash
grep "filterlog.*block" /var/ossec/logs/archives/archives.log | \
  grep -oP 'DPT=\K[0-9]+' | sort | uniq -c | sort -rn
```

---

## ✅ Verification Checklist
- [ ] pfSense sending logs to 192.168.10.10:514
- [ ] Wazuh receiving and parsing pfSense logs
- [ ] Custom rules loaded and active
- [ ] Test alerts triggering correctly
- [ ] Dashboard showing pfSense data
- [ ] Log retention configured (30+ days)

---

## 🔗 Related Documentation
- [pfSense Setup Guide](pfsense-setup.md)
- [Firewall Rules](firewall-rules.md)
- [Wazuh Rule Documentation](../../wazuh/manager/rules/)

---

## 📅 Last Updated
- Date: 2026-02-05
- Status: Draft - implement during lab build
