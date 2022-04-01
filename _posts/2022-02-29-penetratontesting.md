---
title: "Penetration Testing"
date: 2022-3-29 00:10:10 +0530
categories: [Penetration Testing, Methodology]
tags: [ctf, urchin]
image: /assets/img/pentesting/Web-Application-Penetration-Testing.jpg
---

# Introduction 
Am [peterChain](https://twitter.com/peterChain7), A holder of  bachelor in information systems. A hardcore in cybersecurity, My purpose in hacking is like anybodies purpose and all in all is to keep and implement secure systems as possible.\
Am going to introduce to you guys some guides to an overview of penetration testing as a bigginer in Penetration Testing and ethical hacking.

**Why we need Penetration Testing?**

The primary benefit of Penetration Testing is to prevent data from being stolen and misused by malicious attackers, as well as: Discovering vulnerabilities from an attacker's point of view(POV) so that weak points can be fixed. Implementing a secure network that prevents security breaches to keep companies out our cyber attacks that would totally damage the whole company assets.

**Why do we need some guides and methodology?**

-  Methodology gives a penetration tester legitimacy and provides professionally sound findings. It also provides a detailed plan that helps to keep penetration testing on track, making the process smooth, effective and manageable.

-  It will help you to don't miss up any important information that will be needed in the next steps or in your entire work

## How we can perform penetration testing 

-  **Internal Penetration testing**

As the name implies, the internal penetration testing is performed within the organization via LAN, including testing web applications that are hosted on the intranet. It facilitate identification of any vulnerabilities that may exist within the corporate firewall.

  Example.
Some of the internal attacks that can happen include: 

- Malicious Employee Attacks by aggrieved employees, contractors, or other parties who have resigned but still have access to the internal security policies and passwords
- Social Engineering Attacks
- Simulation of Phishing Attacks
- Attacks using User Privileges 
- Careless insider — an innocent employee who unknowingly exposes the system to outside threats. This is the most common type of insider threat, resulting from mistakes, such as leaving a device exposed or falling victim to a scam. For example, an employee who intends no harm may click on an insecure link, infecting the system with malware.

-  **External Penetration testing**

It focuses on attacks initiated from outside the organization to test web applications hosted on the internet. in here you may  have no  information about the internal system and the security layers implemented by the organization.


<div class="overflow-table" markdown="block">

| Form of penetration testing|        description                                |   
| :--------------------------| :-------------------------------------------------| 
|  White box                 | you have  full network and system information     |
|  Grey Box                  | only limited information is shared with the tester|   
|  Black Box                 | No information is provided to the tester at all   |      
     
</div>

  
## Methodology in penetration testing (Black Box)

-  Planning and preparing
-  Footprinting and reconnaissance
-  Scanning  and enumeration
-  Vulnerability Analysis
-  Gaining  access
-  Prevelege escalation
-  Clearing loggs
-  Reporting 

*Am going to provide some practicals and tools for performing each methodlogy* **Stay updated**

**Note** Everyone according to experience in penetration testing may have own methodoly depending on the type of resources being tested and competece in penetration testing. 

### simple brief explanation of each methodology

1. **Planning and preparing**

- Defining the scope and goals of a test, including the systems to be addressed and the testing methods to be used.
- Set goals for overall penetration testing tast
- Conduct a threat model, Here plan and concetrate on targeted areas so as findings more vulnerabilities in short time frame

2. **Footprinting and reconnaissance**

**Note** This is very most important stage in penetration testing, Because it is foundation for all other stages. Failure to perform footprinting and reconnaissance will increate the highest channces of finding *No* or *Very few* Vulnerabilities.
 
 - Here we are going to collect more information about the target network or organization.

  Example.

   - Organization Information

           - Employee details, Partener details, Website links, Web technologies used, Pantents and trade marks

   - Network information

           - Domains, Subdomains, Network blocks, Network Topologies, Trusted routers, Firewalls, Ip addressess of the reachable systems, The who is record, Dns records and other related information.

  - System information

            - Operating  systems, Web servers, User accounts and passwords.


## Lab Tasks on Footprinting and reconnaissance


1. Footprinting through search engines

- Google hacking techniques
- Video search engines
- FTP search engines
- IOT search engines 