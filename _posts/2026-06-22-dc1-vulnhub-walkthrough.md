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
