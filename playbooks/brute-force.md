# Brute Force Attack Lab

## Objective
Simulate and detect brute force authentication attacks using a SOC-style monitoring approach.

This lab focuses on:
- Repeated authentication failures
- Account lockout behavior
- Log correlation and alerting

---

## Attack Description
A brute force attack attempts to gain unauthorized access by repeatedly trying different credentials.

Common targets:
- SSH
- Web login forms
- VPN portals
- Firewall management interfaces

---

## Detection Strategy
The SOC should monitor for:
- High volume of failed login attempts
- Multiple failures from a single IP
- Attempts across multiple accounts
- Login attempts outside normal hours

---

## Tools & Logs
- Linux authentication logs (`/var/log/auth.log`)
- Wazuh rules and alerts
- pfSense firewall logs
- Simulated attack scripts

---

## Expected Alerts
- Excessive authentication failures
- Possible credential stuffing
- Suspicious source IP activity

---

## Mitigation
- Rate limiting
- Account lockout policies
- Firewall blocking
- Multi-factor authentication (MFA)

---

## Notes
All attacks are **simulated** in a controlled lab environment.
No real systems or credentials are used.
