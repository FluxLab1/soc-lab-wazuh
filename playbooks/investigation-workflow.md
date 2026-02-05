# Investigation Workflow Playbook

## Purpose
Provide a structured methodology for investigating security alerts within the SOC environment.

---

## Investigation Phases

### 1. Alert Validation
- Review alert severity and rule triggered
- Verify alert source (agent, firewall, endpoint)
- Check for false positives

---

### 2. Context Collection
- Identify affected host(s)
- Identify user accounts involved
- Review recent activity timelines
- Collect related logs (authentication, process, network)

---

### 3. Indicator Analysis
- IP addresses
- File hashes
- Process names
- Command-line arguments
- Registry or persistence artifacts

---

### 4. Correlation
- Search for similar alerts across the environment
- Identify lateral movement or repeated behavior
- Correlate with firewall and network logs

---

### 5. Impact Assessment
- Determine scope of compromise
- Identify data access or exfiltration
- Assess privilege escalation attempts

---

### 6. Decision
- Close as false positive
- Escalate to Incident Response
- Initiate containment actions

---

## Documentation
- Maintain investigation notes
- Preserve evidence
- Record timestamps and findings
- Attach logs and screenshots

---

## Tools Used
- Wazuh SIEM
- pfSense Firewall logs
- Linux & Windows system logs
- Threat intelligence sources

---

## Notes
Consistent investigation workflows reduce error and improve response quality.
