---
title: "Active Directory"
date: 2022-05-19 00:10:10 +0530
categories: [Active Directory]
tags: [ctf]
image: /assets/img/ad/activedirectory-page.png
---

# Introduction.

## Active Directory.


-  Different from other Microsoft protocol like

i. SMB (Server Message Block (SMB))

```bash
port 139 NetBois
port 445 TCP/IP
```
ii. Session service

```bash
port 139
```
Etc.

`Is a database and set of services that connect users with the network resources they need to get their work done.`
It runs on Windows Server and enables administrators to manage permissions and access
to network resources.

`why AD?`

```console
i. Centralized resources
ii. Security administration. Eg. Single logon for access to global resources.
iii. You can manage AD DS from any computer on the networ
```
`AD DS`
   AD DS provides for security certificates, Single Sign-On (SSO),
LDAP, and rights management.

What Services are Provided in Active Directory Domain Services?
`Domain Services:` Stores data and manages communications
between the users and the DC. This is the primary functionality of AD
DS.

`Certificate Services:` Allows your DC to serve digital certificates,
signatures, and public key cryptography