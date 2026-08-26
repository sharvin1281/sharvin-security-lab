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

```text
Failed password for admin from 198.51.100.23
Failed password for backup from 198.51.100.23
Failed password for oracle from 198.51.100.23.

## SOC Analysis
The repeated failures from the same IP suggest an automated attack rather than a 
legitimate user entering an incorrect password.


