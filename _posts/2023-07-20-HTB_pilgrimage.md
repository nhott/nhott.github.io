---
title: PilgrImage Hack The Box 
date: 2023-07-20 22:50:30 +/-TTTT
categories: [Hack The Box, PilgrImage]
tags: [PilgrImage HTB] 
image:
  path: /assets/img/htb/pilgrimage/logo.jpg
  # alt: Pilgrimage HTB
---


## Work Flow
![]( /assets/img/htb/pilgrimage/workflow.png)

## Enumeration
- Scan with nmap => 2 ports open: 22, 80
![]( /assets/img/htb/pilgrimage/nmap.png)
- Go to browser and connect to the target with port 80
![]( /assets/img/htb/pilgrimage/web.png)
- Recon website, I detected a path .git, so we can use git-dumper to get the resource
![]( /assets/img/htb/pilgrimage/git.png)
![]( /assets/img/htb/pilgrimage/sourcecode.png)
- Review the source, I detected a software ImageMagick with version 7.1.0-49 have multiple vulnerabilities
![]( /assets/img/htb/pilgrimage/imagemagick.png)


## Exploit
 I exploit ImageMagick version 7.1.0-49, I will read file /etc/password so I run cargo run "/etc/passwd", It will create a image.png file.
![]( /assets/img/htb/pilgrimage/ex1.png)
 After, I upload image.png file to website and download image at shrink link
![]( /assets/img/htb/pilgrimage/ex2.png)
![]( /assets/img/htb/pilgrimage/ex3.png)
 Continute, I convert the size image and get information image
![]( /assets/img/htb/pilgrimage/ex4.png)

 I see the raw profile type display hex, so I decrypt this information
![]( /assets/img/htb/pilgrimage/ex5.png)

 With information at index.php, I will read file /var/db/pilgrimage
 ![]( /assets/img/htb/pilgrimage/indexCode.png)
 ![]( /assets/img/htb/pilgrimage/ex6.png)
 ![]( /assets/img/htb/pilgrimage/decrypt.png)
 -> I success get the credential to login to the machine
 ![]( /assets/img/htb/pilgrimage/ssh_login.png)

 
## Escalation
 Use linpeas to recon automation the machine, I get some process run with root permision, so I can use that to escalation to root from emily
![]( /assets/img/htb/pilgrimage/root1.png)
![]( /assets/img/htb/pilgrimage/root2.png)
![]( /assets/img/htb/pilgrimage/root3.png)
 Binwalk has a path traversal vulnerability (link exploit https://www.exploit-db.com/exploits/51249)
![]( /assets/img/htb/pilgrimage/root4.png)
![]( /assets/img/htb/pilgrimage/root5.png)
![]( /assets/img/htb/pilgrimage/root6.png)