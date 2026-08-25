---
layout: post
title: "Security Incident Response Report"
date: 2026-08-26
categories: [Cybersecurity, Incident Response, SOC]
tags: [Cybersecurity, SOC, Log Analysis, Incident Response, SSH, SIEM]
---

# Security Incident Response Report

**Prepared by:** Sharvin Rao Soorithamudu  
**Project Type:** Internship Project

## Overview

This project focuses on security log analysis, incident detection, and incident response.

The objective of the project was to analyze system and authentication logs to identify normal activities, suspicious activities, potential attacks, and possible security incidents.

The analysis was performed using two main log files:

- `auth.log`
- `syslog.log`

The investigation focused on identifying authentication failures, successful logins, suspicious IP addresses, unusual user activities, and other abnormal system behaviour.

The project also involved creating detection rules, classifying incidents based on severity, and developing an incident response workflow.

---

# 1.0 Log Analysis

## 1.1 Introduction

Log analysis is the process of examining system and authentication logs to identify normal operations and detect suspicious activities.

Security analysts use log analysis to discover unauthorized access attempts, security incidents, and potential attacks before they cause significant damage.

In this project, the provided `auth.log` and `syslog.log` files were analyzed to identify both legitimate and malicious activities.

---

## 1.2 Overview of Log Files

| Log File | Purpose |
|---|---|
| `auth.log` | Records authentication-related events such as successful logins, failed logins, SSH access, user sessions, and sudo activities. |
| `syslog.log` | Records operating system events, services, scheduled tasks, and application activities. |

---

## 1.3 Failed Login Attempts

### What was observed?
Example: 

• Multiple "Failed password" entries were identified.  
• Most failed attempts originated from the same IP address.  
• Several different usernames were targeted.  

### Why is this suspicious? 
Repeated failed login attempts often indicate that someone is trying to guess a 
user's password through a brute-force attack. 
 
### Evidence 
Failed password for admin from 198.51.100.23 

Failed password for backup from 198.51.100.23

Failed password for oracle from 198.51.100.23 

### SOC Analysis 
The repeated failures from the same IP suggest an automated attack rather than a 
legitimate user entering an incorrect password. 
