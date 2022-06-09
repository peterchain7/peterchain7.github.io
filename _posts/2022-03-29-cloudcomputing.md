---
title: "cloud computing"
date: 2022-04-03 00:10:10 +0530
categories: [Penetration Testing, Cloud Computing]
tags: [ctf, urchin]
image: /assets/img/cloud/cloud-computing-growth.jpg
---


# Introduction

Am [peterChain](https://twitter.com/peterChain7), Am going to share with you guys about cloud computing and securing cloud services, resources.

## What is cloud computing ❓❓

 *cloud computing* Is the delivery of computing services—including servers, storage, databases, networking, software, analytics, and intelligence—over the Internet (“the cloud”) to offer faster innovation, flexible resources, and economies of scale. You typically pay only for cloud services you use, helping you lower your operating costs, run your infrastructure more efficiently, and scale as your business needs change.

## why use cloud services ❓❓

- [x] cost-effective

Without cloud computing It requires additional investment in hardware, network equipment, software licenses and in-house technician, Cloud computing eliminates the capital expense of buying hardware and software and setting up and running on-site datacenters, So it more economy friendly

 **More and more**

![image](/assets/img/cloud/advantages-of-cloud-computing.png)

## Types of cloud computing

i. Public cloud

Public clouds are owned and operated by a third-party cloud service providers,  like microsoft Azure. Which deliver their computing resources, like servers and storage, over the Internet. 

ii. Private cloud

In private cloud resources are exclusively by a single business or organization. A private cloud can be physically located on the company’s on-site datacenter. Some companies also pay third-party service providers to host their private cloud. A private cloud is one in which the services and infrastructure are maintained on a private network

iii. Hybrid cloud

Hybrid clouds combine public and private clouds, bound together by technology that allows data and applications to be shared between them. By allowing data and applications to move between private and public clouds, a hybrid cloud gives your business greater flexibility, more deployment options, and helps optimize your existing infrastructure, security, and compliance


## Some Types of cloud services: IaaS, PaaS, and SaaS

i. Infrastructure as a service (IaaS)

You rent IT infrastructure—servers and virtual machines (VMs), storage, networks, operating systems—from a cloud provider on a pay-as-you-go basis.

ii. Platform as a service (PaaS)

Platform as a service refers to cloud computing services that supply an on-demand environment for developing, testing, delivering, and managing software applications. PaaS is designed to make it easier for developers to quickly create web or mobile apps, without worrying about setting up or managing the underlying infrastructure of servers, storage, network, and databases needed for development.

iii. Software as a service (SaaS)

Knows *as on-demand software* Web-based/Web-hosted software,Instead of installing and maintaining software, you simply access it via the Internet, freeing yourself from complex software and hardware management.
 
 *more and more*


## cloud computing infrastructure

 Basically it has Front End and Back End

 i. Front End
    
This is a user interface or i can say cloud service user interface that can be used when using cloud services

ii. Back End 

The back end is used by the service provider. It manages all the resources that are required to provide cloud computing services. It includes a huge amount of data storage, security mechanism, virtual machines, deploying models, servers, traffic control mechanisms, etc.

![cloud architecture](/assets/img/cloud/cloud-computing-architecture.png)

## Example of cloud service providers

i. Amazon Web Services (AWS)

ii. Microsoft Azure.

iii. Google Cloud.

iv. Alibaba Cloud.

v.  IBM Cloud.

vi. Oracle.

vii. Salesforce.

viii. SAP.

# Enumeration and Exploitation of cloud services. (Aws S3 Bucket Enumeration)

The first step in penetration testing and ethical hacking is Enumerating the target to get more information and vulnerabilities that may aid you in the next step. 

- [x] **S3** Simple Storage Service, used as storage cloud provided by amazon web service (aws).
- [x] **S3 Buckets** A bucket is a container for objects stored in Amazon S3.  Example of objects are (photos, videos, documents, etc.)

#### Methods and tools for perfoming Enumeration of Aws S3 Bucket Enumeration


- [x] **Google dorking** 

Finding aws buckets through google dorking.

*example*

i. Enumerating passwords of public accessible insecure S3 buckets

  ```bash
  site:s3.amazonaws.com filetype:xls password
  ```

ii. Enumerating S3 buckects of a target company

```bash
site: s3.amazonaws.com <site.com>
```

iii. SQL Dumps stored on AWS S3 Buckets.

```bash
s3 site:amazonaws.com filetype:sql
```

iv. Enumerating public accessible  s3 bucket directories/files

```console
  Inurl: s3.amazonaws.com/legacy/
  Inurl: s3.amazonaws.com/uploads/   
  Inurl: s3.amazonaws.com/backup/   
  Inurl: s3.amazonaws.com/mp3/   
  Inurl: s3.amazonaws.com/movie/   
  Inurl: s3.amazonaws.com/video/   
  inurl: s3.amazonaws.com   
  indexof s3.amazonaws.com
```

- [x] **In souce code of a company website**

example.

i. Right-click on any image  or any  file of the target application and open it in new tab.

```bash
http://abc.s3.amazonaws.com/images/profile.jpeg

```
So from here the *bucket* name is *abc*

- [x] **You can use many online tools which are available on GitHub to find S3 bucket of a website.**

i. lazy s3 
 
 A Ruby based script to brute-force S3 buckets of a target company [chek it here](https://github.com/nahamsec/lazys3)

 Example usage:

 ```bash
 $ ruby lazys3.rb <COMPANY NAME> 
 ```

ii.  S3Scanner

 A tool can scan open buckets and dump data from it. [check it here](https://github.com/sa7mon/S3Scanner),For downloads and usage.
 if you cloned you can run it with python3.


 Example usage:

 ```bash
 python3 s3scanner.py sites.txt
 ```
 *sites.txt* a file conntaing target compay.

iii. Using Gobuster to enumerate available s3 buckets of a target compay

```bash
 gobuster s3 target.txt -w /usr/share/wordlists/dirb/common.txt
 ```
so put the name of your target in *target.txt* file

iv. *CloudBrute* company objects 

[check it here](https://github.com/0xsha/CloudBrute)

Example usage:

```bash
CloudBrute -d target.com -k target -m storage -t 80 -T 10 -w "./data/storage_small.txt"

```

v. S3 Bucket Finder

**more and more**


#### Exploiting misconfigured aws s3 bucket with AWS CLI

It a command line tool for managing aws services and it can be used to control multiple service

Installation.

```bash
 sudo pip3 install awscli 
 ```
 Example usage:

**Note** You need to have aws s3 account before using tool and configure it

```bash
└─$ aws configure
```

 i. list files from bucket found during enumeration

 ```bash
 aws s3 ls s3://abc.example.com
 ```

 ii. Move files from your local machine to aws s3 bucket
 
 ```bash
 asw s3 mv malicious.txt s3://abc.example.com
 ```

 iii. removing files from s3 bucket

 ```bash
 aws s3 rm s3://abc.example.com/malicious.txt
 ```
iv. copying files from s3 bucket

```bash
aws s3 cp <file> s3://<bucket-name>
```

#### AWS AIM 

Aws identity and Access Management (IAM). You can exaploit misconfired IAM user privileges, By exaploiting misconfigured user policy.

Full lab and practise for exaploiting AIM can be found [here, check it](https://attackdefense.pentesteracademy.com/listingnoauth?labtype=cloud-services&subtype=cloud-services-amazon-iam)


**Other tools and security on cloud services**

[other tools](https://github.com/mxm0z/awesome-sec-s3)

[defensive and offensive tools](https://github.com/toniblyx/my-arsenal-of-aws-security-tools)

[cloud service security issues](https://insights.sei.cmu.edu/blog/12-risks-threats-vulnerabilities-in-moving-to-the-cloud/)

[tips to secure yourself](https://medium.com/nerd-for-tech/all-you-need-to-know-about-cloud-computing-and-cyber-security-8a05e5865020)

**Thanks keep an eye on this for more new updates** 

![thanks much](/assets/img/cloud/thank-you-thanks.gif)
