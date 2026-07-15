---
layout: post
title: "DC-1 VulnHub Walkthrough"
date: 2026-07-15
categories: [VulnHub]
tags: [DC-1, Linux, Drupal, CTF]
---

# DC-1 VulnHub Walkthrough

This walkthrough demonstrates how I solved the DC-1 vulnerable machine from VulnHub in a controlled lab environment.
## Introduction

DC-1 is a beginner-friendly vulnerable virtual machine (VM) created for practicing penetration testing techniques in a safe and controlled environment. The machine is available on VulnHub and is designed to teach the complete penetration testing lifecycle, including reconnaissance, enumeration, vulnerability analysis, exploitation, and privilege escalation.

In this walkthrough, I will demonstrate how I identified the target, gathered information, exploited a known Drupal vulnerability, and successfully gained root access. All activities were performed in a local lab environment for educational purposes only.
## Machine Information

| Information | Details |
|-------------|---------|
| Machine | DC-1 |
| Platform | VulnHub |
| Difficulty | Beginner |
| Operating System | Debian Linux |
| Web Server | Apache |
| CMS | Drupal 7 |
| Goal | Obtain Root Access |

## Lab Setup

Before starting the penetration test, I prepared a safe and isolated lab environment to ensure that no real systems were affected during the assessment.

### Environment

| Component | Details |
|----------|---------|
| Attacker Machine | Kali Linux |
| Target Machine | DC-1 (VulnHub) |
| Virtualization Software | VirtualBox |
| Network Configuration | Host-Only Adapter |

The attacker machine (Kali Linux) and the target machine (DC-1) were connected using a Host-Only network, allowing communication between both virtual machines while keeping them isolated from the public internet.

## Reconnaissance

Reconnaissance is the first phase of penetration testing. The goal is to identify the target machine and gather as much information as possible before attempting any exploitation.

Since the DC-1 virtual machine was running inside a VirtualBox Host-Only network, its IP address was initially unknown. Therefore, I performed network discovery from the Kali Linux attacker machine.

### Network Discovery

I used **Netdiscover** to identify active hosts on the local network.

**Command:**

```bash
sudo netdiscover
```

**Explanation**

The scan identified the IP address of the DC-1 machine as **192.168.100.113**, which became the target for the remainder of the penetration test.

![Netdiscover Scan](/assets/img/netdiscover.png)

## Enumeration

After identifying the target IP address, the next step was to enumerate the system to discover open ports, running services, and technologies that could be used as potential attack vectors.

### Port Scanning

I performed a service and version scan using **Nmap** to identify accessible services running on the target machine.

**Command**

```bash
nmap -p- -sV 192.168.100.113
```

**Explanation**

The scan identified HTTP (Port 80) as the primary attack surface. Since the web server was publicly accessible, it became the main focus for further investigation.

![Nmap Scan](/assets/img/nmap.png)

### Web Application Enumeration

After opening the target IP address in a web browser, I discovered that the application was running a Drupal-based website with a login page.

This indicated that the target was using a Content Management System (CMS), which could potentially contain known vulnerabilities if it was running an outdated version.

![Drupal Homepage](/assets/img/drupal-page.png)


### Source Code Inspection

The page source and browser developer tools were inspected to identify technologies used by the application.

Several Drupal-specific JavaScript files were discovered, confirming that the web application was running the Drupal CMS.

![Source Code Inspection](/assets/img/source-code.png)

### Robots.txt Analysis

The robots.txt file was reviewed because it can sometimes reveal directories or files that are not intended for public indexing.

Several Drupal-related directories were listed, providing additional information about the application's structure and confirming that the target was a Drupal installation.

![robots.txt](/assets/img/robots.png)

### Technology Fingerprinting

To gather more information about the target web server, I used WhatWeb.

**Command**

```bash
whatweb http://192.168.100.113
```

**Explanation**

The scan identified Drupal 7 and other web technologies used by the target. Since older versions of Drupal are known to contain critical vulnerabilities, this information was valuable for the next phase of the assessment.

![WhatWeb Scan](/assets/img/whatweb.png)



