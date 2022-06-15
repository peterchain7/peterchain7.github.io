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
signatures, and public key cryptography.

`Lightweight Directory Services:` Supports LDAP for cross platform domain services, like
any Linux computers in your network.

`Directory Federation Services:` Provides SSO authentication for multiple applications in
the same session, so users don’t have to keep providing the same credentials.

`Rights Management:` Controls information rights and data access policies. For
example, Rights Management determines if you can access a folder or send an email.

#### Domain Controller (DC)

Are the servers in your network that host AD DS. DCs respond to authentication requests and store
AD DS data. DCs host other services that are complementary to AD DS as well.

<figure>
<img src="/assets/img/ad/dc.jpeg" alt="Domain controller">
<figcaption>Domain Controller</figcaption>
</figure>

<figure>
<img src="/assets/img/ad/kb.jpeg" alt="kb">
<figcaption>Kerberous Authentication</figcaption>
</figure>


<figure>
<img src="/assets/img/ad/kb2.jpeg" alt="kb">
<figcaption>Kerberous Authentication</figcaption>
</figure>

#### Terms used in AD
- Domain
- Tree
- Forest

##### Active Directory Best Practises

<figure>
<img src="/assets/img/ad/adbest.jpeg" alt="ad best">
<figcaption>Active Directory Best Practise</figcaption>
</figure>

## Attacks Related to Active Directory.


ASREPRoast.

    Attack looks for users without Kerberos pre-authentication required attribute (DONT_REQ_PREAUTH). That means that anyone can send an AS_REQ request to the DC on behalf of any of those users, and receive an AS_REP message.
      A) impacket-GetNPUsers ==> Find users with preauthentication Enabled
    
Kerberos Attacks called ==> Kerberoasting is an attack that abuses the Kerberos protocol to harvest password hashes for Active Directory user accounts with service Principal Name (SPN) values.

     A Service Principal Name (SPN) Is a name in Active Directory that a client uses to uniquely identify an instance of a service. An SPN combines a service name with a computer and user account to form a type of service ID
SPNs are used by Kerberos authentication to associate a service instance with a service logon

	impacket-GetADUsers. ==> Finds for ACtive Directory Users
	impacket-GetNPUsers ==> GEt users with service principal Name registered. 
	Golden SAML Attack.
	Forged Kerberos Tickets.