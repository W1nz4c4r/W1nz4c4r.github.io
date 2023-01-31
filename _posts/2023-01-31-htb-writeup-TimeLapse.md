---
layout: single
title: Timelapse - Hack The Box
excerpt: "Timelapse is considered a easy windows machine, We beging this be finding a pfx file in the shared SMB with null authentification. After getting the file we are able to extract the private key and the cert form it. Then we login as legacyy to the machine using evil-winrm over ssl. To gain access to Administrator we are able to find some credentials that lets us login with the user svc_deploy and using his privilege as LAPS_reader (group) then we are able to get admins 'temp' password"
date: 2023-01-31
classes: wide
header:
  teaser: /assets/images/htb-writeup-Timelapse/timelapse-logo.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:
  - easy
  - windows
  - SMB
  - LAPS
  - OpenSSL

---

![](/assets/images/htb-writeup-Timelapse/timelapse-logo.png)

### Machine IP = 10.10.11.152

## Port Scan
We start with our usual NMAP scan:
* sudo nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.152

```
# Nmap 7.92 scan initiated Wed Jan 25 09:34:05 2023 as: nmap -sS -sV -sC -p- -vvv -oA nmap/allPorts 10.10.11.152
Nmap scan report for 10.10.11.152
Host is up, received echo-reply ttl 127 (0.067s latency).
Scanned at 2023-01-25 09:34:06 EST for 520s
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec      syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2023-01-25 22:41:13Z)
135/tcp   open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 127
464/tcp   open  kpasswd5?         syn-ack ttl 127
593/tcp   open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 127
3268/tcp  open  ldap              syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 127
5986/tcp  open  ssl/http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=dc01.timelapse.htb
| Issuer: commonName=dc01.timelapse.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-10-25T14:05:29
| Not valid after:  2022-10-25T14:25:29
| MD5:   e233 a199 4504 0859 013f b9c5 e4f6 91c3
| SHA-1: 5861 acf7 76b8 703f d01e e25d fc7c 9952 a447 7652
| -----BEGIN CERTIFICATE-----
| MIIDCjCCAfKgAwIBAgIQLRY/feXALoZCPZtUeyiC4DANBgkqhkiG9w0BAQsFADAd
| MRswGQYDVQQDDBJkYzAxLnRpbWVsYXBzZS5odGIwHhcNMjExMDI1MTQwNTI5WhcN
| MjIxMDI1MTQyNTI5WjAdMRswGQYDVQQDDBJkYzAxLnRpbWVsYXBzZS5odGIwggEi
| MA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDJdoIQMYt47skzf17SI7M8jubO
| rD6sHg8yZw0YXKumOd5zofcSBPHfC1d/jtcHjGSsc5dQQ66qnlwdlOvifNW/KcaX
| LqNmzjhwL49UGUw0MAMPAyi1hcYP6LG0dkU84zNuoNMprMpzya3+aU1u7YpQ6Dui
| AzNKPa+6zJzPSMkg/TlUuSN4LjnSgIV6xKBc1qhVYDEyTUsHZUgkIYtN0+zvwpU5
| isiwyp9M4RYZbxe0xecW39hfTvec++94VYkH4uO+ITtpmZ5OVvWOCpqagznTSXTg
| FFuSYQTSjqYDwxPXHTK+/GAlq3uUWQYGdNeVMEZt+8EIEmyL4i4ToPkqjPF1AgMB
| AAGjRjBEMA4GA1UdDwEB/wQEAwIFoDATBgNVHSUEDDAKBggrBgEFBQcDATAdBgNV
| HQ4EFgQUZ6PTTN1pEmDFD6YXfQ1tfTnXde0wDQYJKoZIhvcNAQELBQADggEBAL2Y
| /57FBUBLqUKZKp+P0vtbUAD0+J7bg4m/1tAHcN6Cf89KwRSkRLdq++RWaQk9CKIU
| 4g3M3stTWCnMf1CgXax+WeuTpzGmITLeVA6L8I2FaIgNdFVQGIG1nAn1UpYueR/H
| NTIVjMPA93XR1JLsW601WV6eUI/q7t6e52sAADECjsnG1p37NjNbmTwHabrUVjBK
| 6Luol+v2QtqP6nY4DRH+XSk6xDaxjfwd5qN7DvSpdoz09+2ffrFuQkxxs6Pp8bQE
| 5GJ+aSfE+xua2vpYyyGxO0Or1J2YA1CXMijise2tp+m9JBQ1wJ2suUS2wGv1Tvyh
| lrrndm32+d0YeP/wb8E=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-01-25T22:42:43+00:00; +7h59m58s from scanner time.
|_http-title: Not Found
9389/tcp  open  mc-nmf            syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http        syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49692/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
49701/tcp open  msrpc             syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-01-25T22:42:04
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 19804/tcp): CLEAN (Timeout)
|   Check 2 (port 32357/tcp): CLEAN (Timeout)
|   Check 3 (port 8045/udp): CLEAN (Timeout)
|   Check 4 (port 22941/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: mean: 7h59m57s, deviation: 0s, median: 7h59m57s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jan 25 09:42:46 2023 -- 1 IP address (1 host up) scanned in 520.77 seconds
```

## Enumeration

If we look into the result we are able to find some domains such as **dc01.timelapse.htb** & **timelaps.htb**, which we are going to add to **/etc/hosts**!

![](/assets/images/htb-writeup-Timelapse/pic01.png)

then I go to the port/service that I feel most comfortable with which in this case it will be SMB, we check with smbclient with null auth:
```
smbclient -L \\10.10.11.152\ -N 
```

![](/assets/images/htb-writeup-Timelapse/pic02.png)

When we check the "Shares" folder, we're able to find to folders **dev** & **HelpDesk** if we investigate a little bit more about the file we encounter a .zip file that we download, some LAPS documents and a installer of LAPS .msi.

![](/assets/images/htb-writeup-Timelapse/pic03.png)

we try to unzip the winrm_backup.zip file, but we encounter that the zip  is protected with a password. To crack the password of the zip we can use zip2john but in my case I prefered to use the tool [Fcrackzip](https://www.kali.org/tools/fcrackzip/)

![](/assets/images/htb-writeup-Timelapse/pic04.png)

```
fcrackzip -D -p /usr/share/wordlists/rockyou.txt -u winrm_backup.zip
```
![](/assets/images/htb-writeup-Timelapse/pic05.png)

We are able to crack the password needed for the file:
* supremelegacy

after unzipping the file we find a file named **legacyy_dev_auth.pfx**, if you want to learn more about LAPS on windows visit [Link1](https://www.howtouselinux.com/post/pfx-file-with-examples) or [Link2](https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file)