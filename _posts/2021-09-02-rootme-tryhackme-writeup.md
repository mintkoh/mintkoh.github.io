---
layout: post
title: RootMe - TryHackMe Writeup
subtitle: An easy room
categories: writeup
tags: [linux, file upload bypass, suid]
---
## RootMe - TryHackMe Writeup
[RootMe](https://tryhackme.com/room/rrootme){:target="_blank"} is a TryHackMe's room made by the amazing [ReddyyZ](https://tryhackme.com/p/ReddyyZ){:target="_blank"}. The difficult level is easy and is a linux machine. 
In this room I used many tools like nmap, whatweb, wfuzz or the pentestmonkey reverse shell for the file upload bypass vulnerability found.

This first article explains how I obtained the user and root flag while taking advantage of the vulnerabilities found in the system.

Machine: Linux\
Difficulty: Easy\
Platform: TryHackMe\
Tags: linux, file upload bypass, suid

## Reconnaissance
### Nmap
With the nmap command `nmap -p- --open -T5 -v -n <ip_address>` I found port 22 and 80 open. Then I run `nmap -sC -sV -p22,80 <ip_address>` to use the default scripts to scan the services and see its versions, obtaining the following result:

![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled.png)
### Whatweb and Wappalizer
To get more information about the webserver in port 80 we use `whatweb https://<ip`:
![nmap_result](/assets/images/2021-09-02-rootme-tryhackme-writeup/Untitled 1.png)



