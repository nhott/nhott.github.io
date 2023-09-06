---
title: CozyHosting Hack The Box 
date: 2023-09-04 22:50:30 +/-TTTT
categories: [Hack The Box, CozyHosting]
tags: [CozyHosting HTB] 
image:
  path: /assets/img/htb/cozyhosting/logo.png
---


## Work Flow
![]( /assets/img/htb/cozyhosting/workflow.png)

## Enumeration
 Use nmap to scan this machine. I detect that the machine has two ports 80 and 22 open.
![]( /assets/img/htb/cozyhosting/nmap.png)

 I add the domain to the config file /etc/hosts and go to the browser to connect to the website
![]( /assets/img/htb/cozyhosting/website.png)

 User dirsearch to find the path of the website
![]( /assets/img/htb/cozyhosting/dir.png)

 Access to the path actuator, I see some data. I focus at sessions
![]( /assets/img/htb/cozyhosting/actuator.png)

 With the path session I can get session of kanderson
![]( /assets/img/htb/cozyhosting/session.png)

 Fuzzing more the session I see the path admin
![]( /assets/img/htb/cozyhosting/ffuf.png)



## Exploit
 With all information I gathering, I use session of kanderson to get /admin page
 At here, I can input a IP and username. I the machine try to ssh to my IP input
![]( /assets/img/htb/cozyhosting/loginWithSession.png)
![]( /assets/img/htb/cozyhosting/insertPayload.png)

The username does not contain space characters. So to inject payload reshell I must find the payload bypass reshell.
I saw common limitations bypasses reshell (https://book.hacktricks.xyz/linux-hardening/bypass-bash-restrictions).
Send the request and run listening at attaker machine:
![]( /assets/img/htb/cozyhosting/payload.png)

![]( /assets/img/htb/cozyhosting/nc.png)

  With the file jar I downloaded, I extracked it and see the information postgresSQL at application.properties
![]( /assets/img/htb/cozyhosting/extractFile.png)

![]( /assets/img/htb/cozyhosting/getInfo-psql.png)

 With the information I get, I connect to postgreSQL and gathering information. I see some hash passwd.
 To crack the hash password, I use John The Ripper. And after get password I successfully login ssh
![]( /assets/img/htb/cozyhosting/hashPasswd.png)

![]( /assets/img/htb/cozyhosting/crackPasswd.png)

![]( /assets/img/htb/cozyhosting/ssh.png)

## Escalation
 After login success, I recon with command sudo -l
 ![]( /assets/img/htb/cozyhosting/reconRoot.png)

 I see user josh can sudo ssh. So I search sudo ssh at gtfobins.github.io
 ![]( /assets/img/htb/cozyhosting/root.png)