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
![image]( /assets/img/htb/pilgrimage/workflow.png)

## Enumeration
- Scan with nmap => 2 ports open: 22, 80
![image]( /assets/img/htb/pilgrimage/nmap.png)
- Go to the browser and connect to the target with port 80
![image]( /assets/img/htb/pilgrimage/web.png)
- Recon the website, I detected a path .git, so I used git-dumper to get the resource.
![image]( /assets/img/htb/pilgrimage/git.png)
![image]( /assets/img/htb/pilgrimage/sourcecode.png)
- Review the source, I detected the software ImageMagick with version 7.1.0-49 has multiple vulnerabilities.
![image]( /assets/img/htb/pilgrimage/imagemagick.png)


## Exploit
 I exploit ImageMagick version 7.1.0-49, I will read file /etc/password so I run cargo run "/etc/passwd", It will create a image.png file.
![image]( /assets/img/htb/pilgrimage/ex1.png)
 After I upload an image.png file to the website and download the image at the shrink link.
![image]( /assets/img/htb/pilgrimage/ex2.png)
![image]( /assets/img/htb/pilgrimage/ex3.png)

 Continue, I convert the image size and get image information.
![image]( /assets/img/htb/pilgrimage/ex4.png)

 I see the raw profile type display in hex, so I decrypt this information.
![image]( /assets/img/htb/pilgrimage/ex5.png)

 With information at index.php, I will read file /var/db/pilgrimage
 ![image]( /assets/img/htb/pilgrimage/indexCode.png)
 ![image]( /assets/img/htb/pilgrimage/ex6.png)
 ![image]( /assets/img/htb/pilgrimage/decrypt.png)
 -> I success get the credential to login to the machine
 ![image]( /assets/img/htb/pilgrimage/ssh_login.png)

 
## Escalation
 Use linpeas to automate the machine's reconnaisance, I can get some processes running with root permissions, so I can use that to escalate to root from the emily user.
![image]( /assets/img/htb/pilgrimage/root1.png)
![image]( /assets/img/htb/pilgrimage/root2.png)
![image]( /assets/img/htb/pilgrimage/root3.png)
 Binwalk has a path traversal vulnerability (exploit link: https://www.exploit-db.com/exploits/51249)
![image]( /assets/img/htb/pilgrimage/root4.png)
![image]( /assets/img/htb/pilgrimage/root5.png)
![image]( /assets/img/htb/pilgrimage/root6.png)