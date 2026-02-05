# Escalation Playbook

## Purpose
Define criteria and procedures for escalating security alerts and incidents within the SOC environment.

---

## Escalation Triggers

An alert MUST be escalated if:
- Severity level is High or Critical
- Indicators of Compromise are confirmed
- Multiple systems are affected
- Privileged accounts are involved
- External attacker persistence is detected

---

## Escalation Levels

### Level 1 → Level 2
- Confirmed malicious activity
- Repeated failed authentication attempts
- Malware indicators detected

### Level 2 → Level 3
- Active data exfiltration
- Lateral movement detected
- Domain controller or critical infrastructure involved

---

## Escalation Procedure
1. Document findings in the alert ticket
2. Preserve logs and evidence
3. Notify senior analyst or SOC lead
4. Initiate Incident Response Playbook
5. Maintain chain of custody

---

## Communication
- Use clear, factual language
- Avoid assumptions
- Provide timestamps and affected assets
- Include screenshots or logs when available

---

## Tools Used
- Wazuh Dashboard
- Case management system
- SIEM correlation rules

---

## Notes
Timely escalation prevents impact escalation and limits attacker dwell time.
