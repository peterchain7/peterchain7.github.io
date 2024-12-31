---
title: "Backtrack machine"
date: 2024-12-31 00:10:10 +0530
categories: [Tryhackme, Linux Machine]
tags: [ctf]
image: /assets/img/backtrack/profile.png
---

# Introduction

`Backtrack` is the room created by [tryhackme](https://tryhackme.com/p/tryhackme),[0utc4st](https://tryhackme.com/p/0utc4st) and [YoloSaimo](https://tryhackme.com/p/YoloSaimo)


<br>
 Here is write up on how i successfully compromised and obtainned user and root flags

**LESSON LEARNED IN THIS MACHINE**

>1. Web application Enumeration
>2. Exploitiing `Local File inclusion`
>3. Remote files Enumeration via `LFI`
>4. Exploiting apache Tomcat to `RCE`   
>5. User privilege escalation
>8. Exploiting vulnerable Linux binaries
>9. Root privilege escalation throught TTY push back

### Methodolgy

1. Enumeration and Scanning 
2. Vulnerability Analysis
3. Penetration testing
4. Gaining initial access
5. User prevelege escalation - Pivoting attack
6. Root prevelege escalation
7. Clearing loggs
8. Reporting 
