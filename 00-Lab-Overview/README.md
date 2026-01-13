
# SOC Homelab Overview

## Purpose
This homelab was built to simulate real-world SOC analyst workflows, focusing on log ingestion, threat detection, investigation, and alerting using Splunk.

The goal of this project is to demonstrate hands-on detection engineering and security analysis skills through realistic attack scenarios rather than theoretical explanations.

---

## Lab Architecture

### Virtual Machines
- **Attacker VM**: Kali Linux  
  - Used to simulate external attacks such as brute force, scanning, and web exploitation
- **Victim VM**: Ubuntu Server  
  - Hosts services such as SSH and web applications
  - Generates security-relevant logs (authentication, process execution, network activity)

### Network Design
- Internal Network: Victim ↔ Attacker communication
- NAT: Internet access for updates and tooling

---

## Logging & Telemetry

### Log Sources
- Linux authentication logs (`/var/log/auth.log`)
- SSH daemon logs (`sshd`)
- System logs (`syslog`)

### Log Ingestion
- Logs are forwarded from the Victim VM to Splunk
- Data is indexed under:
  - `index=main`
- Field normalization and extraction are performed where applicable

---

## Detection Platform

### SIEM
- **Splunk Enterprise**
  - Used for log analysis, detection logic, and alerting
  - SPL (Splunk Processing Language) is used to create investigation and detection queries

### Detection Methodology
- Initial investigation performed using ad-hoc SPL and `rex`
- Reusable detections implemented using permanent field extractions
- Threshold-based detections used for brute force and scanning activity

---

## Detection Scenarios

Each scenario in this repository represents a realistic SOC use case and includes:
- Attack simulation
- Investigation queries
- Detection logic
- Screenshots of evidence
- Analyst reasoning and conclusions

Current scenarios include:
1. SSH Brute Force Attack
2. Successful SSH Login (Credential Compromise)
3. Privilege Escalation Attempts
4. Suspicious Command Execution
5. Port Scanning Activity
6. Web Attack Simulation

---

## Skills Demonstrated
- Linux log analysis
- SPL query development
- Field extraction and normalization
- Threat detection and alert logic
- SOC-style investigation and documentation
- GitHub-based security portfolio organization

---

## Notes
This lab is intentionally designed to prioritize detection logic and analyst decision-making over infrastructure complexity.
