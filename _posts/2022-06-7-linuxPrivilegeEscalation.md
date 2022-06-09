---
title: "Linux — Privilege Escalation" 
date: 2022-07-07 14:40:00 +0530
categories: [Red Team, Privilegeescalation]
tags: [ctf,nmap,hackthebox]
image: /assets/img/privilegeescalation/logoLInuxpriv.jpg
---

# Linux Privilege escalation.
> [!TOPIC]
> Linux operating system hacking to gain more permision in the operating system.

>[!NOTE]
>## WHERE TO START?

Prior to doing anything, you need to get an idea of what you are dealing with.

- Kernel information

```bash
uname -a 2>/dev/null
```
- Process information

```bash
cat /proc/cpuinfo 2>/dev/null
```
-  The os version

```bash
cat /etc/*-release 2>/dev/nul
```
- Linux capabilities

```bash
getcap -r / 2>/dev/null
```
- Logged in Users and what they are doing

```bash
whom
w
```
- List current process

```bash
ps au
```
- Find writable directory

```shell-session
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```

<div class="primer-spec-callout success" markdown="1">
  _**Pro tip:** Use this to celebrate an achievement!_
</div>

<!-- <div class="primer-spec-callout success" markdown="1">
_vulnerabilities that can led to Linux Privilege Escalation!_
</div> -->

```bash
-   Kernel exploits
-   Programs running as root
-   Installed software
-   Weak/reused/plaintext passwords
-   Inside service
-   Suid misconfiguration
-   Abusing sudo-rights
-   World writable scripts invoked by root
-   Bad path configuration
-   Cronjobs
-   Unmounted filesystems
-    Vulnerable Services
-    Cron Job Abuse
-    Special Permissions
-    Sudo Rights Abuse
-    Path Abuse
-    Wildcard Abuse
-    Credential Hunting
-    Shared Libraries
-    Shared Object Hijacking
-    Privileged Groups
-    Miscellaneous Techniques
-    Linux Hardening
-    Linux Local Privilege Escalation - Skills Assessment
```


[My reference](https://systemweakness.com/how-to-escalate-privileges-in-linux-privilege-escalation-techniques-70c92499ae45)

### 1. Kernel Exploits
>Targets linux kernel to execute malicios codes in the target system with an eleveted privileges.
This is because kernel is running with higher privileges in the linux operating system.

>[!Work Flow]
> 1. Trick the kernel to run our payload in kkernel mode
> 2. Manipulate kernel data, process privileges 
> 3. Launch a shell with new privileges Get root!

<!-- > conditions for successfully kernel exploit -->


>[!PREVENTION]
>1. Making sure kernel are patched
>2. Making sure remove if not neccessary file transfer programs, like FTP,SCP,TFTP, curl,wget. Or should be run under limited privileges to some users

>[!EXAMPLE]
>**The infamous DirtyCow exploit — Linux Kernel <= 3.19.0–73.8**

A race condition was found in the way the Linux kernel’s memory subsystem handled the copy-on-write (COW) breakage of private read-only memory mappings.

>[!CAUTION]
>## Why you should avoid running any local privilege escalation exploit at first place?

Though, it feels very tempting to just run an exploit and get root access, but you should always keep this as your last option.

1. The remote host might crash as many of the root exploits publicly available are not very stable.
2. You might get root and then crash the box.  
3. The exploit might leave traces/logs that can get you caught.

### 2. Exploiting services which are running as root

>[!example]
>**_Exploiting any service which is running as root will give you Root!_**

The famous [EternalBlue](https://en.wikipedia.org/wiki/EternalBlue) and [SambaCry](https://thehackernews.com/2017/05/samba-rce-exploit.html) exploit, exploited smb service which generally runs as root.￼￼

[EternalBlue](https://en.wikipedia.org/wiki/EternalBlue#cite_note-ars-5) is a computer [exploit](https://en.wikipedia.org/wiki/Exploit_(computer_security) "Exploit (computer security)") developed by the U.S. [National Security Agency](https://en.wikipedia.org/wiki/National_Security_Agency "National Security Agency") (NSA).[[6]](https://en.wikipedia.org/wiki/EternalBlue#cite_note-6) It was leaked by the [Shadow Brokers](https://en.wikipedia.org/wiki/The_Shadow_Brokers "The Shadow Brokers") hacker group on April 14, 2017, one month after Microsoft released patches for the [vulnerability](https://en.wikipedia.org/wiki/Vulnerability_(computing) "Vulnerability (computing)")

> Check services that adminis run as root.

Running service have a greate security risk.
Example.
1. Web servers
2. mail servers
3. database servers

It shows you all the ports open and are listening.
```bash
netstat -antup
```

>[!example]
>**Exploiting a vulnerable version of MySQL which is running as root to get root access**
>
[MySQL UDF Dynamic Library](https://www.exploit-db.com/exploits/1518/) exploit lets you execute arbitrary commands from the mysql shell. If mysql is running with root privileges, the commands will be executed as root.

It shows us the services which are running as root
```bash
 ps -aux | grep root
```

Other example
```bash
select sys_exec('whoami');
select sys_eval('whoami');
```


>[!NOTE]
>One of the biggest mistake web admins do, is to run a webserver with root privilege. A command injection vulnerability on the web application can lead an attacker to root shell. **_This is a classic example of why you should never run any service as root unless really require_**

### 3. Exploiting SUID Executables
SUID means Set User ID, A feature that allows programs to run with root privileges in the system. 

```bash
-rwsr-xr-x–
```

The **s** Means SUID bit, the file can be run with root permision.

>[!NOTE]
>SUID bit should not be set especially on any file editor as an attacker can overwrite any files present on the 

Finding executables with a SUID bit set 

```bash
# Find SUID
find / -perm -u=s -type f 2>/dev/null
# or 
# Find files run by root
find / -user root -perm -u=s -type f 2>/dev/null

# Find GUID
find / -perm -g=s -type f 2>/dev/null
# Find system writable files

find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```


###  5.  Exploiting badly configured cron jobs.

>[!EXAMPLE]
>**_Cron jobs, if not configured properly can be exploited to get root privilege._**

1. Any script or binaries in cron jobs which are writable?  
2. Can we write over the cron file itself.  
3. Is cron.d directory writable?

###### Looking for cronjobs

```bash
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
```

### 6.  World writable scripts invoked as root.

If you can find word writable files by root you can add your own malicious code in that script that when root run it, It will escalate privileges to root. 

```bash
# World writable files directories

find / -writable -type d 2>/dev/null
find / -perm -222 -type d 2>/dev/null
find / -perm -o w -type d 2>/dev/null

# World executable folder
find / -perm -o x -type d 2>/dev/null

# World writable and executable folders
find / \( -perm -o w -perm -o x \) -type d 2>/dev/null
```

