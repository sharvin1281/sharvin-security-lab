---
title: "Find Anyone's Location Using OSINT"
date: 2026-07-21
categories:
  - OSINT
  - Cybersecurity
tags:
  - OSINT
  - Hound
  - Kali Linux
  - Browser Fingerprinting
  - Information Gathering
  - Cloudflare
toc: true
comments: true
---

# Find Anyone's Location Using OSINT

## Introduction

Open Source Intelligence (OSINT) is the process of collecting publicly available information to support cybersecurity investigations, digital forensics, and security assessments.

In this lab, I explored **Hound**, an educational OSINT tool that demonstrates how publicly available browser information and user-granted permissions can reveal valuable metadata about a device.

The objective of this exercise was to understand how information gathering works from an attacker's perspective and, more importantly, how defenders can recognize and mitigate these risks.

> **Disclaimer**
>
> This lab was performed in a controlled environment using my own devices for educational purposes only. The techniques demonstrated in this article should only be used on systems you own or have explicit authorization to test.

---

# Lab Objectives

- Understand OSINT information gathering
- Learn how browser fingerprinting works
- Observe the information exposed through a web browser
- Analyze collected metadata
- Understand defensive measures against information leakage

---

# Lab Environment

| Component | Details |
|-----------|---------|
| Operating System | Kali Linux |
| Tool | Hound |
| Tunnel Service | Cloudflare Tunnel |
| Browser | Google Chrome |
| Purpose | Educational OSINT Lab |

---

# Step 1: Download the Hound Repository

The first step was to clone the official Hound repository from GitHub into the Kali Linux machine.

This downloads all the required files needed to run the tool locally.

## Command

```bash
cd ~/Music
git clone https://github.com/techchipnet/hound
```

After cloning the repository, navigate into the project folder.

```bash
cd hound
```

## Screenshot

![Cloning the Hound Repository](/assets/osint/03.jpg)

---

# Step 2: Verify the Repository Contents

After entering the project directory, I listed the available files to ensure the repository was downloaded successfully.

## Command

```bash
ls
```

The output shows the main Hound script along with its supporting files.

## Screenshot

![Repository Contents](/assets/osint/04.jpg)

---

# Explanation

At this stage, Hound has not been executed yet. The repository has simply been downloaded to the local machine. Before running the script, Linux requires executable permission to be assigned to the main script.

# Step 3: Configure Permissions and Launch Hound

Before running the Hound tool, I configured the required file permissions and then executed the script.

## Commands

```bash
chmod +w hound.sh
chmod +x hound.sh
bash hound.sh
```

### Command Explanation

- `chmod +w hound.sh` grants write permission to the script.
- `chmod +x hound.sh` grants execute permission so Linux can run the script.
- `bash hound.sh` starts the Hound application using the Bash shell.

> **Note:** Normally, only `chmod +x` is required to execute the script. The `chmod +w` command was included because it was used during this lab.

### Screenshot

![Configuring Permissions and Launching Hound](/assets/osint/05.jpg)

---

# Step 4: Hound Interface

After executing the script, the Hound interface was displayed in the terminal. This interface provides the available tunneling options and prepares the local web server for information gathering.

At this stage, the tool is ready to generate a public URL using a tunneling service.

### Screenshot

![Hound Main Interface](/assets/osint/06.jpg)

---

## Analysis

The Hound interface confirms that the tool has started successfully. The next step is to select a tunneling service, such as **Cloudflare Tunnel**, to create a temporary public URL that can be accessed from another device for testing purposes.

# Step 5: Launch Hound and Select Cloudflare Tunnel

After launching Hound, the main interface was displayed in the terminal. The tool provides several tunneling options that can be used to expose the local web server to the internet.

For this lab, I selected **Cloudflare Tunnel** because it generates a temporary public HTTPS URL without requiring port forwarding or additional network configuration.

## Available Tunnel Options

```text
[01] Localhost
[02] Ngrok
[03] Cloudflare
```

I selected:

```text
03
```

## Why Cloudflare Tunnel?

Cloudflare Tunnel was chosen because it offers several advantages:

- Generates a temporary public HTTPS URL
- No router port forwarding required
- Secure encrypted connection
- Quick and simple setup for testing purposes

### Screenshot

![Hound Interface and Cloudflare Selection](/assets/osint/07.jpg)

---

## Analysis

After selecting Cloudflare Tunnel, Hound initialized the tunnel service and began creating a temporary public URL. This URL would be used in the next step to access the hosted page from another device.

# Step 6: Collect Device and Geolocation Information

After accessing the generated Cloudflare URL from my test device, Hound successfully collected information that was publicly available through the browser. This included browser metadata, device information, and geolocation data after location permission was granted.

> **Note:** This demonstration was performed using my own device in a controlled lab environment for educational purposes only.

---

## 6.1 Device Information

The first result displayed detailed information about the device and browser used to access the generated URL.

The collected information included:

- Public IP Address
- Browser Name
- Operating System
- Device Platform
- Browser Language
- Screen Resolution
- CPU Cores
- RAM Information
- Cookies Status
- User Agent

This information can be useful during reconnaissance because it helps identify the target's operating system, browser version, and hardware characteristics.

### Screenshot

![Device Information](/assets/osint/08.jpg)

---

## 6.2 Geolocation Information

After location permission was granted, Hound successfully retrieved the device's approximate geographic location.

The collected information included:

- Latitude
- Longitude
- Google Maps URL

This demonstrates how browser permissions can reveal sensitive location information when users allow websites to access their location.

### Screenshot

![Geolocation Information](/assets/osint/09.jpg)

---

## Analysis

This exercise demonstrates how publicly available browser metadata and user-granted permissions can expose valuable information. While modern browsers require users to approve location access, attackers may exploit social engineering techniques to persuade victims to grant these permissions.

Understanding how these techniques work helps cybersecurity professionals identify reconnaissance activities, investigate phishing incidents, and educate users about safe browsing practices.
