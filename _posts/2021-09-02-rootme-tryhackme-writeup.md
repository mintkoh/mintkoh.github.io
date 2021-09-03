---
layout: post
title: RootMe - TryHackMe Writeup
subtitle: An easy room
tags: [linux, file upload bypass, suid]
---
## RootMe - TryHackMe Writeup
[RootMe](https://tryhackme.com/room/rrootme){:target="_blank"} is a TryHackMe's room made by the amazing [ReddyyZ](https://tryhackme.com/p/ReddyyZ){:target="_blank"}. The difficult level is easy and is a Linux machine. 
In this room, I used many tools like Nmap, WhatWeb, Wfuzz, or Pentestmonkey's reverse shell for the file upload bypass vulnerability found.

This first article explains how I obtained the user and root flag while taking advantage of the vulnerabilities found in the system.

Machine: Linux\
Difficulty: Easy\
Platform: TryHackMe\
Tags: linux, file upload bypass, suid

## Reconnaissance
### Nmap
With the nmap command `nmap -p- --open -T5 -v -n <ip_address>` I found port 22 and 80 open. Then I run `nmap -sC -sV -p22,80 <ip_address>` to use the default scripts to scan the services and see its versions, obtaining the following result:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled.png)

### Whatweb and Wappalyzer
I executed `whatweb https://<ip_address>` to get more information about the webserver in port 80:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 1.png)

Another option is Wappalyzer, which also uncovers the technologies used on websites:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 3.png)

### Wfuzz

This tool is used to search for hidden content, such as files and directories, which I did use with the following command and found the resources called css, js, panel, and uploads:
`wfuzz -c -L -t 400 --hc=404 -w /usr/share/wordlists/dirb/common.txt http://<ip_address>/FUZZ`

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 2.png)

There was a file upload page in /panel and /uploads possibly show the files uploaded on the server.

## Exploitation 

So the first thing that came to mind was to try if the site is vulnerable to file upload bypass vulnerability and upload a reverse shell to enter the system.

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 4.png)

I downloaded [Pentestmonkey's reverse shell file](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php){:target="_blank"} because the website uses PHP, edited the file to changed the IP address and port to my Kali's IP address and the port I used to establish a reverse shell listener. The listener was set with `nc -lvnp <port>`.
I tried to upload the file, but the server rejected it.  I substituted the .php format into .phtml, and it worked!. 

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 5.png)
![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 6.png)

I went to `http://<ip_address>/uploads/php_reverse_shell.phtml` in the browser to execute it in the server and got a reverse shell being user www-data:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 7.png)

The user flag was found with `find / -name user.txt 2>/dev/null`:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 8.png)

## Privilege escalation

I didn't end here. The next step was to become the user with the most privileges, the root user. And to make it happen, I searched binaries with the SUID bit set:
`find / -perm -4000 2>/dev/null`

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 9.png)

Python was on the list, so I searched for the command to take advantage of the SUID file in [GTFOBins](https://gtfobins.github.io/){:target="_blank"} and got root: `python -c 'import os; os.execl("/bin/sh", "sh", "-p")'`

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 10.png)


See ya!