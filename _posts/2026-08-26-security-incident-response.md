---
title: "Security Incident Response and Log Analysis"
date: 2026-08-26
categories:
  - Cybersecurity
  - Security Operations
  - Incident Response
tags:
  - Cybersecurity
  - Log Analysis
  - Incident Response
  - SOC
  - SSH
  - Brute Force
  - Authentication Logs
toc: true
comments: true
---

# Security Incident Response and Log Analysis

## Introduction

Log analysis is the process of examining system and authentication logs to identify normal operations and detect suspicious activities.

Security analysts use log analysis to discover unauthorized access attempts, security incidents, and potential attacks before they cause significant damage.

In this project, the provided `auth.log` and `syslog.log` files were analyzed to identify both legitimate and potentially malicious activities.

The investigation focused on authentication events, failed login attempts, successful logins, suspicious IP addresses, unusual user activities, and other abnormal behaviour observed within the logs.

> **Disclaimer**
>
> This project was performed for educational and cybersecurity training purposes. The analysis was conducted using the provided log files in a controlled environment. The techniques and findings discussed in this report should only be applied to systems and data for which proper authorization has been obtained.

---

## 1.0 Log Analysis


## 1.1 Overview of the Log Files

The following log files were used during the investigation.

| Log File | Purpose |
|-----------|---------|
| `auth.log` | Records authentication-related events such as successful logins, failed logins, SSH access, user sessions, and sudo activities. |
| `syslog.log` | Records operating system events, services, scheduled tasks, and application activities. |

### `auth.log`

The `auth.log` file contains authentication-related events. These records are useful for investigating login activity, SSH access, failed authentication attempts, successful authentication, user sessions, and privilege-related activities.

### `syslog.log`

The `syslog.log` file contains general operating system and service-related events. These records can provide additional context about system operations, services, scheduled tasks, and application activities.

---

# Analysis

The combination of `auth.log` and `syslog.log` provides a broader view of system activity.

The authentication log is particularly useful for identifying possible unauthorized access attempts, while the system log provides additional information about normal system operations and other events occurring on the machine.

This initial analysis provides the foundation for identifying suspicious activities in the following sections.

## 1.2 Failed Login Attempts

# What Was Observed?

Multiple failed login attempts were identified during the analysis of the authentication logs.

# Example

- Multiple "Failed password" entries were identified.
- Most failed attempts originated from the same IP address.
- Several different usernames were targeted.

# Why Is This Suspicious?

Repeated failed login attempts often indicate that someone is trying to guess a user's password through a brute-force attack.

# Evidence


Failed password for admin from 198.51.100.23

Failed password for backup from 198.51.100.23

Failed password for oracle from 198.51.100.23.

# SOC Analysis
The repeated failures from the same IP suggest an automated attack rather than a 
legitimate user entering an incorrect password.

## 1.3 Successful Logins

# What Was Observed?

The logs contained successful authentication events.

### Examples Include

| Authentication Event | User |
|----------------------|------|
| Accepted publickey | aritra |
| Accepted password | backup |
| Accepted password | sysupdate |

# Normal vs Suspicious

| Normal Activity | Suspicious Activity |
|----------------|---------------------|
| Public key login by authorized users | Successful login immediately after numerous failed attempts |
| Login during expected working hours | Successful login from an attacking IP |

# Why Is It Important?

A successful login following repeated failed attempts may indicate that the attacker successfully guessed the user's password.

## 1.4 Suspicious IP Addresses

# What Was Observed?

One IP address repeatedly attempted to access multiple user accounts.

### Example

| Suspicious IP Address |
|-----------------------|
| 198.51.100.23 |

# Why Is This Suspicious?

A legitimate user normally accesses only their own account.

An attacker commonly:

- Tries multiple usernames
- Performs password guessing
- Continues until a login succeeds

# Possible Impact

| Impact | Description |
|--------|-------------|
| Unauthorized access | An attacker may gain access to an account without authorization. |
| Account compromise | User accounts may be compromised if the attacker successfully authenticates. |
| Data theft | Compromised accounts may be used to access or steal sensitive information. |

## 1.5 Unusual User Activities

## What Was Observed?

The logs showed attempts to access multiple accounts using the same IP address.

### Example Usernames

| Username |
|----------|
| admin |
| postgres |
| oracle |
| backup |
| sysupdate |

## Why Is This Suspicious?

Normal users already know their username.

Attackers often test many common usernames before guessing passwords.

This behavior is called **username enumeration**.

## 1.6 Other Abnormal Behaviour

# Examples

The following abnormal behaviours were identified during the log analysis:

- Login after many failed attempts.
- Multiple accounts accessed from one IP.
- Continuous login attempts within a short period.
- Unexpected authentication methods.
- Unusual login times (if present).

---

## 1.7 Summary of Log Analysis

The following table summarizes the activities identified during the log analysis and their classifications.

| Activity | Classification | Reason |
|----------|----------------|--------|
| CRON Jobs | Normal | Scheduled system maintenance |
| DHCP Renewal | Normal | Automatic network operation |
| Reload Nginx | Normal | Service configuration reload |
| Public Key Login | Normal | Secure authentication |

## 2.0 Detection Logic

Detection logic defines the conditions used to identify potentially suspicious authentication activities. The following rules were developed based on the log analysis performed in this project.

# Rule 1

### Detection Rule

If more than 5 failed login attempts occur from the same IP address within 10 minutes, generate an alert.

### Reason

Repeated failed logins often indicate a brute-force password attack.

### Severity

**Medium**

---

# Rule 2

### Detection Rule

If an IP attempts to log in using more than 5 different usernames, generate an alert.

### Reason

Attackers commonly enumerate usernames before attempting password attacks.

### Severity

**Medium**

---

# Rule 3

### Detection Rule

If a successful login occurs immediately after multiple failed login attempts from the same IP, generate a High severity alert.

### Reason

This may indicate the attacker successfully guessed the user's password.

### Severity

**High**

---

# Rule 4

### Detection Rule

If one IP successfully logs into multiple user accounts within a short period, generate an alert.

### Reason

This behaviour may indicate compromised credentials or lateral movement.

### Severity

**High**

---

# Rule 5

### Detection Rule

If an administrator account logs in outside normal working hours, for example between 12:00 AM and 5:00 AM, generate an alert.

### Reason

Administrative logins outside business hours may indicate unauthorized access.

### Severity

**Medium**

---

# Detection Rules Summary

| Rule | Detection Condition | Reason | Severity |
|------|----------------------|--------|----------|
| Rule 1 | More than 5 failed login attempts from the same IP within 10 minutes | Possible brute-force password attack | Medium |
| Rule 2 | More than 5 different usernames attempted from one IP | Possible username enumeration | Medium |
| Rule 3 | Successful login immediately after multiple failed attempts from the same IP | Possible password compromise | High |
| Rule 4 | One IP successfully logs into multiple user accounts within a short period | Possible compromised credentials or lateral movement | High |
| Rule 5 | Administrator login between 12:00 AM and 5:00 AM | Possible unauthorized administrative access | Medium |
| Failed Login Attempts | Suspicious | Possible brute-force attack |
| Invalid Usernames | Suspicious | Username enumeration |
| Successful Login After Failures | Suspicious | Possible account compromise |
| Same IP Repeated Attempts | Suspicious | Automated attack |

## 4.0 Detection Logic

The incident response process follows a structured workflow consisting of six stages:

**Detection → Alert → Investigation → Response → Recovery → Closure**

---

# Step 1 – Detection

Security monitoring tools identify suspicious activities from the logs, such as repeated failed logins, invalid usernames, or successful logins after many failed attempts.

**Output:** Suspicious activity is detected.

---

# Step 2 – Alert

Detection rules trigger an alert in the Security Information and Event Management (SIEM) system.

The alert contains information such as:

- Source IP address
- Username
- Timestamp
- Event type
- Severity level

**Output:** SOC analyst is notified.

---

# Step 3 – Investigation

The SOC analyst reviews the logs to determine whether the alert represents a genuine security incident.

The investigation includes:

- Reviewing authentication logs
- Checking login history
- Identifying affected accounts
- Correlating related events
- Assessing the scope of the incident

**Output:** Incident confirmed or dismissed as a false positive.

---

# Step 4 – Response

If malicious activity is confirmed, immediate actions are taken to contain the threat.

Possible actions include:

- Blocking the attacker's IP address
- Resetting compromised passwords
- Disabling affected accounts
- Isolating compromised systems
- Escalating the incident to senior analysts

**Output:** Threat contained.

---

# Step 5 – Recovery

Affected systems are restored to a secure state.

Typical recovery actions include:

- Verifying system integrity
- Restoring services if needed
- Applying security patches
- Re-enabling user accounts after verification
- Confirming that no malicious activity remains

**Output:** Normal business operations resume securely.

---

# Step 6 – Closure

After the incident is resolved, the SOC team documents the findings and lessons learned.

The closure process includes:

- Completing the incident report
- Recording the root cause
- Updating detection rules
- Recommending security improvements
- Closing the incident ticket

**Output:** Incident formally closed, with improvements in place to reduce the likelihood of similar attacks in the future.

---

# Incident Response Workflow

| Stage | Main Activity | Output |
|-------|---------------|--------|
| Detection | Identify suspicious activities from logs | Suspicious activity detected |
| Alert | Detection rules trigger a SIEM alert | SOC analyst notified |
| Investigation | Analyze logs and determine whether the alert is genuine | Incident confirmed or dismissed |
| Response | Contain and respond to the confirmed threat | Threat contained |
| Recovery | Restore affected systems to a secure state | Normal operations resume |
| Closure | Document findings and lessons learned | Incident formally closed |


## 5.0 Conclusion

This project provided practical experience in analyzing security logs to distinguish between normal and suspicious activities.

Through the analysis, I identified potential security incidents such as failed login attempts, SSH brute-force attacks, suspicious IP addresses, and successful logins after multiple failed attempts.

I also developed basic detection rules, classified incidents based on their severity, and recommended appropriate response actions following the incident response lifecycle.

From this project, I learned how a Security Operations Center (SOC) analyst monitors system logs, investigates security events, assesses risks, and responds to potential threats.

Overall, this project strengthened my understanding of log analysis, incident detection, incident response, and the importance of documenting security incidents in a professional manner.
