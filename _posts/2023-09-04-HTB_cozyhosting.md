---
title: CozyHosting Hack The Box 
date: 2023-09-04 22:50:30 +/-TTTT
categories: [Hack The Box, CozyHosting]
tags: [CozyHosting HTB] 
image:
  path: /assets/img/htb/cozyhosting/logo.png
---


## Work Flow
![image]( /assets/img/htb/cozyhosting/workflow.png)

## Enumeration
 Use nmap to scan this machine. I detect that the machine has two ports 80 and 22 open.
![image]( /assets/img/htb/cozyhosting/nmap.png)

 I add the domain to the config file /etc/hosts and go to the browser to connect to the website
![image]( /assets/img/htb/cozyhosting/website.png)

 Use the dirsearch to find the path of the website
![image]( /assets/img/htb/cozyhosting/dir.png)

 I have access to the path actuator and I can see some data. I focusing on sessions
![image]( /assets/img/htb/cozyhosting/actuator.png)

 With the path session I can get a session of kanderson
![image]( /assets/img/htb/cozyhosting/session.png)

 Fuzzing the session more, I see the admin path.
![image]( /assets/img/htb/cozyhosting/ffuf.png)



## Exploit
 With all the information I have gathered, I used a session of kanderson to get to the /admin page.

 Here, I can input an IP and username. I the machine will try to ssh to my input IP.
![image]( /assets/img/htb/cozyhosting/loginWithSession.png)
![image]( /assets/img/htb/cozyhosting/insertPayload.png)

The username does not contain any space characters. Therefore, to inject a payload reshell I must find a payload that bypass the reshell.

I saw some common limitations bypasses reshell (https://book.hacktricks.xyz/linux-hardening/bypass-bash-restrictions).
Send the request and run a listener on the attacker machine:
![image]( /assets/img/htb/cozyhosting/payload.png)

![image]( /assets/img/htb/cozyhosting/nc.png)

  With the file jar I downloaded, I extracted it and saw the information for PostgresSQL in application.properties
![image]( /assets/img/htb/cozyhosting/extractFile.png)

![image]( /assets/img/htb/cozyhosting/getInfo-psql.png)

 With the information I get, I connect to PostgreSQL and gather information. I see some hash passwords.

 To crack the hash passwords, I use John The Ripper. And after get the password I successfully login to ssh.
![image]( /assets/img/htb/cozyhosting/hashPasswd.png)

![image]( /assets/img/htb/cozyhosting/crackPasswd.png)

![image]( /assets/img/htb/cozyhosting/ssh.png)

## Escalation
 After login successfully, To escalation to root, I check the permissions of the user with the command "sudo -l"
 ![image]( /assets/img/htb/cozyhosting/reconRoot.png)

 I saw that user josh can use sudo ssh. So I searched for "sudo -> ssh" at gtfobins.github.io
 ![image]( /assets/img/htb/cozyhosting/root.png)