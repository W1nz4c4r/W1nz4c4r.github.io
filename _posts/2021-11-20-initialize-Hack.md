---
layout: single
title: Python script - initialize Hack
excerpt: "initHACK.py is s python3 script I made for myself and its the first step I usually do when I'm on CTF enviroments. The script is divided in 3 parts, the first part is making a ping to the attacked machine and it tell the user if the machine is active and also what machine is (windows, linux). The second part creates 4 folders I always use --> exploits, content, nmap, notes. the final part just copies to the clipboard a nmap I always use to scan an ip."
date: 2021-11-20
classes: wide
header:
  teaser: /assets/images/python-scripts/python-logo.jpeg
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - python
tags:  
  - Hacking
---

![](/assets/images/python-scripts/initHACK.png)

initHACK.py is s python3 script I made for myself and its the first step I usually do when I'm on CTF enviroments. The script is divided in 3 parts, the first part is making a ping to the attacked machine and it tell the user if the machine is active and also what machine is (windows, linux). The second part creates 4 folders I always use --> exploits, content, nmap, notes. the final part just copies to the clipboard a nmap I always use to scan an ip.

To have a faster access to the script I place it on /usr/bin.

If you want to Clone this repository please refer to: https://github.com/W1nz4c4r/initHACK



```
python3

#!/usr/bin/python3
#-------Author-------------
#Ricardo Morales (W1nz4c4r)
#--------------------------
import sys
import subprocess
import re

def showInfo(value, ttl):
    if value == 1:
        print("\t [+] Linux machine --> ttl {}".format(ttl))
    elif value == 2:
        print("\t [+] Windows machine --> ttl {}".format(ttl))



def whichSystem(IP):
     try:
         #running the ping -c1 IP and checking the ttl to define the system
         process = subprocess.Popen(["ping -c1 {}".format(IP)], stdout=subprocess.PIPE, stderr=subprocess.PIPE, shell=True)
         result, error = process.communicate()
         result = result.decode("utf-8")
         result = result.split()
         result = result[12].split('=')
         result = int(result[1])
         #checking the ttl
         if result >= 0 and result <= 64:
             #Linux machine
             showInfo(1, result)
             create_Workspace()
             ip_portScan(IP)
         elif result >= 65 and result <= 128:
             #Windows machine
             showInfo(2, result)
             create_Workspace()
             ip_portScan(IP)
         else:
             print("Error Please check the IP")
             sys.exit(1)
     except:
         print("[-] Check your input!")
         sys.exit(1)

def create_Workspace():
    print("[+] Creating folders...")
    #making dir's (Content, nmap and exploits)
    #making nmap
    nmap_Proc = subprocess.run(["mkdir nmap"], shell=True)
    #making contect forlder
    cont_Proc = subprocess.run(["mkdir content"], shell=True)
    #making exploits forlder
    exp_Proc = subprocess.run(["mkdir exploits"], shell=True)
    print("[+] Creating folders => DONE")

def ip_portScan(IP):
    print("[+] Nmap command copied to the clipboard")
    command = "nmap -sS -sV -sC -p- -vvv {} -oA allPorts ".format(IP)
    subprocess.run("xclip -sel clip", universal_newlines=True, input=command, shell=True)




def main(IP):
    print("\n[+] Checking the IP : {}".format(IP))
    whichSystem(IP)




if __name__ == '__main__':
    if len(sys.argv) == 2:
        IP = sys.argv[1]
        main(IP)
    else:
        print("usage: python3 rams.py <IP>")
        print("example: python3 rams.py 192.163.0.25")
        sys.exit(1)

```
