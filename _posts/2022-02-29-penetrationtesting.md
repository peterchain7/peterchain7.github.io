---
title: "Penetration Testing"
date: 2022-3-29 00:10:10 +0530
categories: [Penetration Testing, Methodology]
tags: [ctf, urchin]
image: /assets/img/pentesting/Penetrationtesting.png
---

# Introduction

Am [peterChain](https://twitter.com/peterChain7), A holder of bachelor degree in information systems. A hardcore in cybersecurity, My purpose in cybersecurity is like anybodies purpose and all in all is to keep and implement secure systems as possible.
Am going to introduce to you guys some guides to an overview of penetration testing and ethical hacking.

**Why we need Penetration Testing?**

The primary benefit of Penetration Testing is to prevent data from being stolen and misused by malicious attackers, as well as: Discovering vulnerabilities from an attacker's point of view(POV) so that weak points can be fixed. Implementing a secure network that prevents security breaches to keep companies out our cyber attacks that would totally damage the whole company assets.

**Why do we need some guides and methodology?**

- Methodology gives a penetration tester legitimacy and provides professionally sound findings. It also provides a detailed plan that helps to keep penetration testing on track, making the process smooth, effective and manageable.

- It will help you to don't miss up any important information that will be needed in the next steps or in your entire work

## How we can perform penetration testing

- **Internal Penetration testing**

As the name implies, the internal penetration testing is performed within the organization via LAN, including testing web applications that are hosted on the intranet. It facilitate identification of any vulnerabilities that may exist within the corporate firewall.

  Example.
Some of the internal attacks that can happen include:

- Malicious Employee Attacks by aggrieved employees, contractors, or other parties who have resigned but still have access to the internal security policies and passwords
- Social Engineering Attacks
- Simulation of Phishing Attacks
- Attacks using User Privileges
- Careless insider — an innocent employee who unknowingly exposes the system to outside threats. This is the most common type of insider threat, resulting from mistakes, such as leaving a device exposed or falling victim to a scam. For example, an employee who intends no harm may click on an insecure link, infecting the system with malware.

- **External Penetration testing**

It focuses on attacks initiated from outside the organization to test web applications hosted on the internet. in here you may  have no  information about the internal system and the security layers implemented by the organization.

<div class="overflow-table" markdown="block">

| Form of penetration testing|        description                                |
| :--------------------------| :-------------------------------------------------|
|  White box                 | you have  full network and system information     |
|  Grey Box                  | only limited information is shared with the tester|
|  Black Box                 | No information is provided to the tester at all   |

</div>

## Methodology in penetration testing (Black Box)

- Planning and preparing
- Footprinting and reconnaissance
- Scanning  and enumeration
- Vulnerability Analysis
- Gaining  access
- Prevelege escalation
- Clearing loggs
- Reporting

*Am going to provide some practicals and tools for performing each methodlogy* **Stay updated**

**Note** Everyone according to experience in penetration testing may have own methodoly depending on the type of resources being tested and competece in penetration testing.


### simple brief explanation of each methodology


- [x] 1. **Planning and preparing**

 i. Defining the scope and goals of a test, including the systems to be addressed and the testing methods to be used.\
 ii. Set goals for overall penetration testing tast\
 iii. Conduct a threat model, Here plan and concetrate on targeted areas so as findings more vulnerabilities in short time frame


- [x] 2. **Footprinting and reconnaissance**

**Note** This is very most important stage in penetration testing, Because it is foundation for all other stages. Failure to perform footprinting and reconnaissance will increate the highest channces of finding *No* or *Very few* Vulnerabilities.
 
 - Here we are going to collect more information about the target network or organization.

  Example.

- Organization Information

           - Employee details, Partener details, Website links, Web technologies used, Pantents and trade marks

- Network information

           - Domains, Subdomains, Network blocks, Network Topologies, Trusted routers, Firewalls, Ip addressess of the reachable systems, The who is record, Dns records and other related information.

- System information

            - Operating  systems, Web servers, User accounts and passwords.

**Overview of Footprinting**

Footprinting refers to the process of collecting information about a target network and its environment, which helps in evaluating the security posture of the target organization’s IT infrastructure. It also helps to identify the level of risk associated with the organization’s publicly accessible information.

Footprinting can be categorized into passive footprinting and active footprinting:

1. Passive Footprinting: Involves gathering information without direct interaction. This type of footprinting is principally useful when there is a requirement that the information-gathering activities are not to be detected by the target.

2. Active Footprinting: Involves gathering information with direct interaction. In active footprinting, the target may recognize the ongoing information gathering process, as we overtly interact with the target network.

#### Lab 1. Tasks on Footprinting and reconnaissance

1. Footprinting through search engines

- Gather information using advanced Google hacking techniques

Google Hacking involves an attacker submitting queries to Google's search engine with the intention of finding sensitive information residing on Web pages that have been indexed by Google, or finding sensitive information with respect to vulnerabilities in applications indexed by Google. [readmore here](https://securitytrails.com/blog/google-hacking-techniques)

- Gather Information from Video search engines

 Getting more information from videos uploaded by an organization. Example you can get these in youtube posts, just copy the *link location* of video and user site like [citizenevidence](https://citizenevidence.amnestyusa.org), [Google videos](https://video.google.com), Video analysis tool such as [EZGif](https://ezgif.com)

- Gather Information from  FTP search engines

 File Transfer Protocol (FTP) search engines are used to search for files located on the FTP servers.these files may hold valuable information about the target organization. Many industries, institutions, companies, and universities use FTP servers to keep large file archives and other software that are shared among their employees. FTP search engines provide information about critical files and directories, including valuable information such as business strategies, tax documents, employee’s personal records, financial records, licensed software, and other confidential information. EXample Enter company name in sites like [searchftps](https://www.searchftps.net/) to find any useful information

- Gather Information from IoT Search Engines.

 IoT search engines crawl the Internet for IoT devices that are publicly accessible. These search engines provide crucial information, including control of SCADA (Supervisory Control and Data Acquisition) systems, traffic control systems, Internet-connected household appliances, industrial appliances, CCTV cameras, etc. Site like [shodan](https://www.shodan.io) just enter company name in search bar.

##### Lab 2: Perform Footprinting Through Web Services

**Lab Scenario**

As a professional ethical hacker or pen tester, you should be able to extract a variety of information about your target organization from web services. By doing so, you can extract critical information such as a target organization’s domains, sub-domains, operating systems, geographic locations, employee details, emails, financial information, infrastructure details, hidden web pages and content, etc.

Using this information, you can build a hacking strategy to break into the target organization’s network and can carry out other types of advanced system attacks.

**Lab Objectives**

- Find the Company’s Domains and Sub-domains using Netcraft
- Gather Personal Information using PeekYou Online People Search Service
- Gather an Email List using theHarvester
- Gather Information using Deep and Dark Web Searching
- Determine Target OS Through Passive Footprinting

#### Lab 3: Perform Footprinting Through Social Networking Sites

**Lab Scenario**

As a professional ethical hacker, during information gathering, you need to gather personal information about employees working in critical positions in the target organization; for example, the Chief Information Security Officer, Security Architect, or Network Administrator. By footprinting through social networking sites, you can extract personal information such as name, position, organization name, current location, and educational qualifications. Further, you can find professional information such as company or business, current location, phone number, email ID, photos, videos, etc. The information gathered can be useful to perform social engineering and other types of advanced attacks.

**Lab Objectives**

- Gather employees’ information from LinkedIn using theHarvester
- Gather personal information from various social networking sites using Sherlock
- Gather information using Followerwonk

#### Lab 4: Perform Website Footprinting

**Lab Scenario**

As a professional ethical hacker, you should be able to extract a variety of information about the target organization from its website; by performing website footprinting, you can extract important information related to the target organization’s website such as the software used and the version, operating system details, filenames, paths, database field names, contact details, CMS details, the technology used to build the website, scripting platform, etc. Using this information, you can further plan to launch advanced attacks on the target organization.

**Lab Objectives**

- Gather information about a target website using ping command line utility
- Gather information about a target website using Central Ops
- Extract a company’s data using Web Data Extractor
- Mirror the target website using HTTrack Web Site Copier
- Gather a wordlist from the target website using CeWL

#### Lab 5: Perform Email Footprinting

**Lab Scenario**

As a professional ethical hacker, you need to be able to track emails of individuals (employees) from a target organization for gathering critical information that can help in building an effective hacking strategy. Email tracking allows you to collect information such as IP addresses, mail servers, OS details, geolocation, information about service providers involved in sending the mail etc. By using this information, you can perform social engineering and other advanced attacks.

**Lab Objectives**

- Gather information about a target by tracing emails using eMailTrackerPro

#### Lab 6: Perform Whois footprinting

**Lab Scenario**

During the footprinting process, gathering information on the target IP address and domain obtained during previous information gathering steps is important. As a professional ethical hacker or penetration tester, you should be able to perform Whois footprinting on the target; this method provides target domain information such as the owner, its registrar, registration details, name server, contact information, etc. Using this information, you can create a map of the organization’s network, perform social engineering attacks, and obtain internal details of the network.

**Lab Objectives**

- Perform Whois lookup using DomainTools

#### Lab 7: Perform DNS Footprinting

**Lab Scenario**

As a professional ethical hacker, you need to gather the DNS information of a target domain obtained during the previous steps. You need to perform DNS footprinting to gather information about DNS servers, DNS records, and types of servers used by the target organization. DNS zone data include DNS domain names, computer names, IP addresses, domain mail servers, service records, and much more about a target network.

Using this information, you can determine key hosts connected in the network and perform social engineering attacks to gather even more information.

**Lab Objectives**

- Gather DNS information using nslookup command line utility and online tool
- Perform reverse DNS lookup using reverse IP domain check and DNSRecon

#### Lab 8: Perform Network Footprinting

**Lab Scenario**

With the IP address, hostname, and domain obtained in the previous information gathering steps, as a professional ethical hacker, your next task is to perform network footprinting to gather the network-related information of a target organization such as network range, traceroute, TTL values, etc. This information will help you to create a map of the target network and perform a man-in-the-middle attack.

**Lab Objectives**

- Locate the network range
- Perform network tracerouting in Windows and Linux Machines

**End of some note about Footprinting and reconnaissance,** *Next Scanning and enumeration*
