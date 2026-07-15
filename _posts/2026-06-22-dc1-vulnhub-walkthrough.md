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

## Vulnerability Analysis

After identifying the target as a Drupal 7 web application, the next step was to determine whether any publicly known vulnerabilities affected the installed version.

### Exploit Research

I used **Searchsploit** to search Exploit-DB for publicly available exploits related to Drupal 7.

**Command**

```bash
searchsploit drupal 7
```

**Explanation**

The search returned several Drupal-related exploits. Among them, **Drupalgeddon2 (CVE-2018-7600)** was identified as the most suitable attack vector because it allows unauthenticated remote code execution (RCE) on vulnerable Drupal installations.

![Searchsploit Results](/assets/img/searchsploit.png)

### Why Drupalgeddon2?

Drupalgeddon2 (CVE-2018-7600) is a critical Remote Code Execution (RCE) vulnerability affecting vulnerable Drupal 7 installations. It allows an attacker to execute arbitrary commands on the server without authentication.

Since the target machine was running an outdated version of Drupal, this vulnerability provided a reliable path to gain initial access.

> **Key Finding**
>
> The vulnerability assessment confirmed that the target was susceptible to **Drupalgeddon2 (CVE-2018-7600)**, making it possible to proceed with remote exploitation.


## Exploitation

After confirming that the target was vulnerable to Drupalgeddon2 (CVE-2018-7600), I proceeded with exploitation using the Metasploit Framework. This framework provides a reliable exploit module for vulnerable Drupal installations.

### Starting Metasploit

The first step was to launch the Metasploit Framework.

**Command**

```bash
msfconsole
```

**Explanation**

Metasploit loaded successfully and provided the environment required to configure and execute the Drupal exploit.

![Metasploit Framework](/assets/img/metasploit-frame.png)

### Searching for the Exploit Module

To locate the appropriate exploit, I searched the Metasploit module database.

**Command**

```bash
search drupal
```

**Explanation**

Among the available modules, the **Drupal Drupalgeddon2 Remote Code Execution** exploit was selected because it targets the vulnerability identified during the analysis phase.

![Metasploit Search](/assets/img/search-drupal.png)

### Configuring the Exploit

After selecting the exploit module, I configured the required options before launching the attack.

**Commands**

```bash
use exploit/unix/webapp/drupal_drupalgeddon2
set RHOSTS 192.168.100.113
run
```

**Explanation**

The target IP address was configured using the `RHOSTS` parameter. Once all required options were set, the exploit was executed against the vulnerable Drupal server.

![Drupalgeddon2 Exploit](/assets/img/config.png)

### Successful Exploitation

The exploit completed successfully and established a Meterpreter reverse shell session.

This provided initial access to the target machine, allowing further post-exploitation and privilege escalation activities.

**Evidence**

- Target IP configured successfully
- Meterpreter session established
- Remote command execution achieved

![Meterpreter Session](/assets/img/meterperter.png)

> **Key Finding**
>
> Successful exploitation of the Drupalgeddon2 vulnerability resulted in remote access to the target system through a Meterpreter session.


## Post-Exploitation

After obtaining a Meterpreter session, I performed post-exploitation activities to gather additional information about the target system and identify possible privilege escalation vectors.

### Upgrading to an Interactive Shell

To improve shell stability and gain access to standard Linux commands, I upgraded the Meterpreter shell to a fully interactive Bash shell.

**Command**

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

**Explanation**

The upgraded shell provided a more stable environment for system enumeration and privilege escalation.

![Shell Upgrade](/assets/img/shell.png)

### SUID Enumeration

To identify potential privilege escalation opportunities, I searched the system for SUID-enabled binaries.

**Command**

```bash
find / -perm -u=s -type f 2>/dev/null
```

**Explanation**

Several SUID binaries were identified during enumeration. One of the most interesting findings was **/usr/bin/find**, which is known to have privilege escalation techniques documented on GTFOBins.

![SUID Enumeration](/assets/img/suid.png)

## Privilege Escalation

After identifying the SUID-enabled `find` binary, I used a GTFOBins technique to obtain root privileges.

### Exploiting the SUID Find Binary

**Commands**

```bash
find / -name index.php -exec "/bin/sh" \;
whoami
```

**Explanation**

The `find` binary was executed with elevated privileges, spawning a root shell. Running the `whoami` command confirmed that the current user was **root**.

![Root Access](/assets/img/root.png)

> **Key Finding**
>
> The misconfigured SUID `find` binary allowed privilege escalation to root, resulting in full administrative control of the target system.


## Final Flag Retrieval

After obtaining root access, I navigated to the root user's directory and retrieved the final flag.

**Commands**

```bash
cd /root
ls
cat thefinalflag.txt
```

**Explanation**

Successfully retrieving the final flag confirmed that the penetration test had achieved its objective and that the target machine had been fully compromised.

![Final Flag](/assets/img/final-flag.png)

## Recommendations

Based on the findings during this assessment, the following security improvements are recommended:

- Keep Drupal and all installed modules updated with the latest security patches.
- Remove unnecessary files that expose application information.
- Restrict access to sensitive directories and configuration files.
- Regularly audit SUID and SGID binaries to prevent privilege escalation.
- Apply the Principle of Least Privilege (PoLP) for all users and services.
- Perform regular vulnerability assessments and penetration testing.

## Conclusion

This walkthrough demonstrated the complete penetration testing lifecycle against the DC-1 vulnerable machine, beginning with reconnaissance and ending with full root compromise.

Throughout the assessment, I identified the target, enumerated services, analyzed vulnerabilities, exploited the Drupalgeddon2 (CVE-2018-7600) vulnerability, performed post-exploitation activities, escalated privileges using a misconfigured SUID binary, and successfully retrieved the final flag.

DC-1 is an excellent beginner-friendly machine for learning web application exploitation, Linux enumeration, and privilege escalation in a controlled environment. It provides valuable hands-on experience and reinforces the importance of secure system configuration, timely patching, and the principle of least privilege.




