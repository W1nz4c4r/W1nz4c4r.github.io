---
layout: single
title: Survellance - Hack The Box
excerpt: "Survellance is a medium machine of Hack The Box (HTB), the machine  begins with identifying a CMS vulnerability on the webpage hosted on port 80, which grants initial access to the system. Through enumeration, I uncovered a database file containing an encrypted password. Cracking this password allows me to access a ZoneMinder instance running on localhost. By exploiting a known vulnerability in ZoneMinder, I elevate my access to the 'zoneminder' user. The final step involves leveraging sudo privileges to achieve full root access"
date: 2023-02-13
classes: wide
header:
  teaser: /assets/images/htb-writeup-survellance/Surveillance-LOGO.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox 
  - Medium
  - Linux

tags:
  - CMS
  - Craft CMS
  - unauth-RCE
  - CVE-2023-41892
  - Port fowarding
  - CVE-2023-26035

---
![survellance Logo](/assets/images/htb-writeup-survellance/Surveillance-LOGO.png)

# enumeration 

## Port Scan

First start with the *NMAP* scan:

```
# Nmap 7.94SVN scan initiated Thu Mar 21 14:48:00 2024 as: nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.245
Nmap scan report for 10.10.11.245
Host is up, received reset ttl 63 (0.058s latency).
Scanned at 2024-03-21 14:48:01 EDT for 56s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN+/g3FqMmVlkT3XCSMH/JtvGJDW3+PBxqJ+pURQey6GMjs7abbrEOCcVugczanWj1WNU5jsaYzlkCEZHlsHLvk=
|   256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIm6HJTYy2teiiP6uZoSCHhsWHN+z3SVL/21fy6cZWZi
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://surveillance.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Mar 21 14:48:57 2024 -- 1 IP address (1 host up) scanned in 57.52 seconds
```

From the scan and with some help of *whatweb* I am able to see that the page is redirecting me to *surveillance.htb* so I proceed to add it to /etc/hosts

```bash
whatweb surveillance.htb
```

![Alt text](/assets/images/htb-writeup-survellance/surv1.png)

By checking the result of *whatweb* I can see from the begginning that we will be dealing with a Content Management System (CMS), being more specific in this case we'll be dealing with **Craft CMS**

# FootHold

I decided to check the page hosted in port 80, to see if i can find something else that can help us find out more about the CMS that we are working with .