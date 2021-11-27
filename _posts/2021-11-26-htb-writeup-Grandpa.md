---
layout: single
title: Grandpa - Hack The Box
#excerpt: "Delivery is a quick and fun easy box where we have to create a MatterMost account and validate it by using automatic email accounts created by the OsTicket application. The admins on this platform have very poor security practices and put plaintext credentials in MatterMost. Once we get the initial shell with the creds from MatterMost we'll poke around MySQL and get a root password bcrypt hash. Using a hint left in the MatterMost channel about the password being a variation of PleaseSubscribe!, we'll use hashcat combined with rules to crack the password then get the root shell."
date: 2021-11-26
classes: wide
header:
  teaser: /assets/images/htb-writeup-Grandpa/granpa-logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:  
  - osticket
  - mysql
  - mattermost
  - hashcat
  - rules
---

![](/assets/images/htb-writeup-Grandpa/granpa-logo.png)

Delivery is a quick and fun easy box where we have to create a MatterMost account and validate it by using automatic email accounts created by the OsTicket application. The admins on this platform have very poor security practices and put plaintext credentials in MatterMost. Once we get the initial shell with the creds from MatterMost we'll poke around MySQL and get a root password bcrypt hash. Using a hint left in the MatterMost channel about the password being a variation of PleaseSubscribe!, we'll use hashcat combined with rules to crack the password then get the root shell.
