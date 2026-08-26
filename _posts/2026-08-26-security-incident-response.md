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

# 1.0 Log Analysis

## 1.1 Introduction

Log analysis is an important activity in cybersecurity because system logs provide valuable information about what is happening within an operating system and its services.

By examining authentication and system events, security analysts can identify normal system behaviour as well as suspicious activities that may indicate an attempted or successful attack.

For this project, two primary log files were analyzed:

- `auth.log`
- `syslog.log`

The analysis was performed to distinguish between legitimate system activities and suspicious authentication behaviour.

---

## 1.2 Overview of the Log Files

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

## Analysis

The combination of `auth.log` and `syslog.log` provides a broader view of system activity.

The authentication log is particularly useful for identifying possible unauthorized access attempts, while the system log provides additional information about normal system operations and other events occurring on the machine.

This initial analysis provides the foundation for identifying suspicious activities in the following sections.

# 1.3 Failed Login Attempts

## What Was Observed?

Multiple failed login attempts were identified during the analysis of the authentication logs.

### Example

- Multiple "Failed password" entries were identified.
- Most failed attempts originated from the same IP address.
- Several different usernames were targeted.

## Why Is This Suspicious?

Repeated failed login attempts often indicate that someone is trying to guess a user's password through a brute-force attack.

## Evidence


Failed password for admin from 198.51.100.23

Failed password for backup from 198.51.100.23

Failed password for oracle from 198.51.100.23.

## SOC Analysis
The repeated failures from the same IP suggest an automated attack rather than a 
legitimate user entering an incorrect password.

# 1.4 Successful Logins

## What Was Observed?

The logs contained successful authentication events.

### Examples Include

| Authentication Event | User |
|----------------------|------|
| Accepted publickey | aritra |
| Accepted password | backup |
| Accepted password | sysupdate |

## Normal vs Suspicious

| Normal Activity | Suspicious Activity |
|----------------|---------------------|
| Public key login by authorized users | Successful login immediately after numerous failed attempts |
| Login during expected working hours | Successful login from an attacking IP |

## Why Is It Important?

A successful login following repeated failed attempts may indicate that the attacker successfully guessed the user's password.

# 1.5 Suspicious IP Addresses

## What Was Observed?

One IP address repeatedly attempted to access multiple user accounts.

### Example

| Suspicious IP Address |
|-----------------------|
| 198.51.100.23 |

## Why Is This Suspicious?

A legitimate user normally accesses only their own account.

An attacker commonly:

- Tries multiple usernames
- Performs password guessing
- Continues until a login succeeds

## Possible Impact

| Impact | Description |
|--------|-------------|
| Unauthorized access | An attacker may gain access to an account without authorization. |
| Account compromise | User accounts may be compromised if the attacker successfully authenticates. |
| Data theft | Compromised accounts may be used to access or steal sensitive information. |

# 1.6 Unusual User Activities

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

# 1.7 Other Abnormal Behaviour

## Examples

The following abnormal behaviours were identified during the log analysis:

- Login after many failed attempts.
- Multiple accounts accessed from one IP.
- Continuous login attempts within a short period.
- Unexpected authentication methods.
- Unusual login times (if present).

---

# 1.8 Summary of Log Analysis

The following table summarizes the activities identified during the log analysis and their classifications.

| Activity | Classification | Reason |
|----------|----------------|--------|
| CRON Jobs | Normal | Scheduled system maintenance |
| DHCP Renewal | Normal | Automatic network operation |
| Reload Nginx | Normal | Service configuration reload |
| Public Key Login | Normal | Secure authentication |
| Failed Login Attempts | Suspicious | Possible brute-force attack |
| Invalid Usernames | Suspicious | Username enumeration |
| Successful Login After Failures | Suspicious | Possible account compromise |
| Same IP Repeated Attempts | Suspicious | Automated attack |


