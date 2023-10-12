---
title: Zipping Hack The Box 
date: 2023-08-31 22:50:30 +/-TTTT
categories: [Hack The Box, Zipping]
tags: [Zipping HTB] 
image:
  path: /assets/img/htb/zipping/logo.png
---


## Work Flow
![image]( /assets/img/htb/zipping/workflow.png)

## Enumeration
 Use nmap to scan this machine. I detect that the machine has two ports 80 and 22 open.
![image]( /assets/img/htb/zipping/nmap.png)

 Go to the browser to connect to the website
![image]( /assets/img/htb/zipping/website.png)

 Use the dirsearch to find the path of the website
![image]( /assets/img/htb/zipping/dirsearch.png)


## Exploit
 At the path uploads, I will upload a reshell, but the required upload file is a zip file containing the pdf file. After uploading it will create a link, So I will doubled extension of the reshell file and edited the hex of the A value to 00
![image]( /assets/img/htb/zipping/file_payload.png)
![image]( /assets/img/htb/zipping/upload.png)
![image]( /assets/img/htb/zipping/edit_upload.png)

 Access the URL created after uploading the file. I successfully uploaded the shell file. (Note: you must access it quickly because the uploaded file will be deleted quickly)
![image]( /assets/img/htb/zipping/getShell.png)

 Run the reshell command and run a listener on the attacker machine, I can connect to the machine and get the user.txt
![image]( /assets/img/htb/zipping/reshell.png)
![image]( /assets/img/htb/zipping/nc.png)
## Escalation

 After login successfully, To escalation to root, I check the permissions of the user with the command "sudo -l"
![image]( /assets/img/htb/zipping/root_recon.png)

 When I running stock, it required entering a password. So, to find the password, I downloaded the stock file and used IDA to read the file. I saw a function called checkAuth and I was able to obtain the password.
![image]( /assets/img/htb/zipping/root_findPasswd.png)

 When I enter the password, I have three options to choose.
 It is not a possilility, I tried to find another way to exploit it and I found the link https://tbhaxor.com/exploiting-shared-library-misconfigurations/

![image]( /assets/img/htb/zipping/root_option.png)
![image]( /assets/img/htb/zipping/root_exploit.png)
![image]( /assets/img/htb/zipping/root.png)