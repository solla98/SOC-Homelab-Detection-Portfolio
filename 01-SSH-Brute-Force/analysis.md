# Analysis – SSH Brute Force Attack

## Overview
Multiple failed SSH authentication attempts were observed against a single Linux host within a short time window.  
The activity originated from a single external source IP and targeted multiple authentication attempts, indicating a likely brute force attack.

---

## Initial Observation
- Repeated "Failed password" events from `sshd`
- High frequency of attempts within one-minute intervals
- No corresponding successful login from the same source IP

---

## Investigation Approach
The investigation focused on identifying:
- Source IP addresses generating repeated authentication failures
- Frequency of failed attempts over time
- Potential escalation to successful authentication

Temporary field extraction using `rex` was performed to isolate:
- Source IP (`src_ip`)
- Targeted username (`user`)

---

## Findings
- One source IP generated 5 or more failed login attempts within a 1-minute window
- Attempts targeted privileged accounts such as `admin`
- No evidence of successful authentication from the same source IP

---

## Impact Assessment
While no successful compromise was observed, this activity represents:
- Credential brute force attempts
- Elevated risk to externally exposed SSH services
- Potential precursor to successful compromise if controls are weak

---

## Limitations & Assumptions
- SSH service was intentionally exposed for lab simulation
- Detection threshold may require tuning in production environments

---

## Analyst Conclusion
The observed activity is consistent with SSH brute force behavior and warrants alerting and response actions, including:
- Source IP blocking
- SSH hardening (key-based auth, rate limiting)

