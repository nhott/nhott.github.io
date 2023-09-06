---
title: Keeper Hack The Box 
date: 2023-08-14 22:50:30 +/-TTTT
categories: [Hack The Box, Keeper]
tags: [Keeper HTB] 
image:
  path: /assets/img/htb/keeper/logo.png
---


## Work Flow
![]( /assets/img/htb/keeper/workflow.png)

## Enumeration
 Use nmap to scan this machine. I detect that the machine has two ports 80 and 22 open.
![]( /assets/img/htb/keeper/nmap.png)

 I add the domain to the config file /etc/hosts and go to the browser to connect to the website
![]( /assets/img/htb/keeper/website.png)

## Exploit

I know that this website is Request Tracker (RT) and that it has a form login, so I searched for the account default of RT 4.4.4

I get the defautlt information account for RT 4.4.4 with User: root and Password: password

Use this information, I successfully logged into the website.

![]( /assets/img/htb/keeper/user.png)

I Continued reviewing the website and I saw some users and some information about it. I tried to login to the machine using the information I saw and I successfully SSH to the machine.

![]( /assets/img/htb/keeper/user_info.png)

![]( /assets/img/htb/keeper/login.png)

## Escalation

After login success, I saw a zip file so I downloaded it and extract it.

![]( /assets/img/htb/keeper/download.png)

![]( /assets/img/htb/keeper/extract.png)

Use KeePassXC tool to open passcodes.kdbx. But I don't have password to unclock it, I must crack password. I used keepass2john tool to crack so it need many time so I search vulnerability of keepass and I see a vulnerability with CVE-2023-32784. I git clone code and dump passsword.
![]( /assets/img/htb/keeper/keepassxc.png)

![]( /assets/img/htb/keeper/dumpPasswd.png)

 Some character cannot be found in the dump, so I search google to find the word have mean. I see the word Rødgrød Med Fløde but I can't unclock, with the word at dump I try lowcase charecter rødgrød med fløde and I successfully open passcodes.kdbx file
![]( /assets/img/htb/keeper/search.png)

![]( /assets/img/htb/keeper/readPasscode.png)

I get some information of user root. I try this password to login root but error notification "Configured password was not accepted"
![]( /assets/img/htb/keeper/putty_passwd.png)

I created a key.ppk file to authentication root user. I use this file to config auth credentical when ssh root and I successed login with root user.
![]( /assets/img/htb/keeper/key_auth.png)

![]( /assets/img/htb/keeper/putty_key.png)
