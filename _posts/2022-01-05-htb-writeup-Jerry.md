---
layout: single
title: Jerry - Hack The Box
excerpt: Jerry is an easy machine that as don't require privilege escalation. For this machine we just need to get access to the apache tomcat manager site with default credentials and then upload a war file to get access to the machine as the nt authority\system
date: 2022-01-05
classes: wide
header:
  teaser: /assets/images/htb-writeup-Jerry/Jerry-Logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - OSCP
  - Beginner Track
tags:  
  - windows
  - easy
  - Apache tomcat
---

![](/assets/images/htb-writeup-Jerry/Jerry-Logo.png)


**Machine IP = 10.10.10.95**

To begin with this machine we need to start with some basic steps by sending the *nmap* commands checking the network:
  * nmap -sS -sV -sC -p- -vvv 10.10.10.95 -oA nmap/allPorts

```
# Nmap 7.92 scan initiated Mon Jan  3 23:54:04 2022 as: nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.10.95
Nmap scan report for 10.10.10.95
Host is up, received echo-reply ttl 127 (0.055s latency).
Scanned at 2022-01-03 23:54:04 EST for 251s
Not shown: 65534 filtered tcp ports (no-response)
PORT     STATE SERVICE REASON          VERSION
8080/tcp open  http    syn-ack ttl 127 Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/7.0.88
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Jan  3 23:58:15 2022 -- 1 IP address (1 host up) scanned in 250.71 seconds

```

## Enumeration

As we can see the only port open is the port 8080 and it is an *http* service. We go to http://10.10.10.95:8080/ and we find ourselves on the apache tomcat default page.

![](/assets/images/Beginner-Track/pic1.png)

By default when I see this tomcat page I'll go to http://10.10.10.95:8080/manager or the other option is to click on **Manager App** button. This will prompt us for some credentials, some of the default credentials you can find them [here](https://github.com/netbiosX/Default-Credentials/blob/master/Apache-Tomcat-Default-Passwords.mdown).

The credentials I used in this case to get access where:
  * **user** = tomcat
  * **password** = s3cret

![](/assets/images/Beginner-Track/pic2.png)

After entering the correct credentials we got access to **tomcat web application manager**

![](/assets/images/Beginner-Track/pic3.png)

## Gaining FoodHold

To gain access to the machine all that is need to be done is to upload a war file that connects to our machine. To create this file we will use msfvenom to create the *reverse shell* file .war

![](/assets/images/Beginner-Track/pic4.png)

Then, we need to upload the revshell.war

![](/assets/images/Beginner-Track/pic5.png)

To gain access to the machine all we need to do is to activate the on your machine **nc** on the desired port and then access to http://10.10.10.95/revshell/

![](/assets/images/Beginner-Track/pic6.png)

**You are in as nt authority system**.To fing *both* flags you need to go to the Administrator Desktop and you will find *2 for the price of 1.txt*

![](/assets/images/Beginner-Track/pic7.png)
