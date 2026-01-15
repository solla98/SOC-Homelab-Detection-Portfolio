
# Scenario 3: Privilege Escalation Attempt (Unauthorized Sudo Usage)

## Overview
This scenario simulates a **privilege escalation attempt** in which an attacker, after gaining access to a Linux system with a **non-privileged user account**, attempts to execute commands using `sudo`.
The objective is to detect and analyze **unauthorized sudo activity** from authentication logs and correlate the behavior using Splunk.

---

## Objective
1. Detect sudo execution attempts by a **non-privileged account**
2. Analyze Linux authentication logs for privilege escalation indicators
3. Correlate events in Splunk to support SOC investigation
4. Demonstrate SOC-style triage and analysis methodology

---

## Lab Environment
- **Attacker VM:** Kali Linux
- **Victim VM:** Ubuntu Server
- **Service:** OpenSSH (sshd)
- **Log Source:** `/var/log/auth.log`
- **SIEM:** Splunk
- **User Account:** `user1` (no sudo privileges)

---

## Attack Simulation
### Initial Access
The attacker successfully authenticates to the system using SSH with a standard user account.

- Account: `user1`
- Authentication method: SSH password login
- Result: Login successful

### Privilege Escalation Attempt
After gaining access, the attacker attempts to execute privileged commands using `sudo`.

Commands executed:
```
sudo -l
sudo whoami
```

Expected Behavior:
- Commands fail due to lack of sudo privileges
- Unauthorized sudo attempts are logged

---

## Log Evidence (Victim VM)

Relevant entries observed in `/var/log/auth.log`:
- Successful SSH login session for `user1`
- Unauthorized sudo attempts:
  - `command not allowed`
  - `user NOT in sudoers`

These entries indicate a **privilege escalation attempt by a non-privileged account.**

---

## Splunk Investigation
To investigate potential privilege escalation attempts, sudo-related authentication logs were queried in Splunk.

### Query used:
```spl
index=* source="/var/log/auth.log"
| search _raw="*sudo:*user1*"
| table _time host _raw
```

### Purpose:
- Identify sudo execution attempts by a non-privileged user
- Reduce noise by focusing on a specific account involved in the incident
  
---

## SOC Analysis 
From a SOC perspective:
- The account involved does not belong to the sudoers group
- The commands executed are commonly used for privilege discovery
- Unauthorized sudo attempts indicate a potential privilege escalation attempt
- Further investigation is required to determine intent and scope

---

## Key Findings
- A non-privileged account attempted to execute sudo commands
- The system correctly denied privilege escalation
- Logs provide clear evidence for detection and investigation
- Splunk enables efficient identification of suspicious sudo behavior

---

## Skills Demonstrated
- Linux privilege escalation detection
- Authentication log analysis
- SIEM query development (Splunk)
- SOC triage and investigation workflow
- Security event contextual analysis

---
## Conclusion
This scenario demonstrates how a SOC analyst can identify and investigate unauthorized privilege escalation attempts by analyzing Linux authentication logs and correlating events within a SIEM platform.


## Screenshots
Screenshots of Splunk search results and detection output can be found in the `screenshots/` directory.

### Attack Simulation-ssh login
![Attack Simulation-ssh login](screenshots/01_ssh_login_admin_success.png)

### Attack Simulation-command execution
![Attack Simulation-command execution](screenshots/02_suspicious_command_execution.png)

### Victim Authentication Logs
![Victim Authentication Logs](screenshots/03_sudo_suspicious_commands_log.png)

### Investigation Results 
![Investigation_Query](screenshots/04_splunk_investigation_broad_search.png)

### Detection Results
![Detection Results](screenshots/05_splunk_detection_suspicious_command_execution02.png)
