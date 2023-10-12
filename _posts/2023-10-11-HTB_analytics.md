---
title: Analytics Hack The Box 
date: 2023-10-11 22:50:30 +/-TTTT
categories: [Hack The Box, Analytics]
tags: [Analytics HTB] 
image:
  path: /assets/img/htb/analytics/logo1.png
---


## Work Flow
![]( /assets/img/htb/analytics/workflow.png)

## Enumeration
 Use nmap to scan this machine. I detect that the machine has two ports 80 and 22 open.
![]( /assets/img/htb/analytics/nmap.png)

 Go to the browser to connect to the website
![]( /assets/img/htb/analytics/website.png)
 When I login, it redirects to a subdomain. Add this subdomain to the /etc/passwd file

 Use dirsearch to discover the path of the subdomain on the website
![]( /assets/img/htb/analytics/dirsearch.png)

 I have access to the subdomain path and I know this is an open source Metabse. Let's try to find vulnerabilities in Metabase




## Exploit

![]( /assets/img/htb/analytics/web2.png)
 I found that Metabase has a vulnerability CVE-2023-38646, allow attackers to execute arbitraty commands on the server
 Link POC https://github.com/robotmikhro/CVE-2023-38646

![]( /assets/img/htb/analytics/exploit1.png)
 After obtaining a reverse shell, I couldn't find the user.txt file. I then proceeded to gather information on this machine and discovered an account that can be used for SSH.

![]( /assets/img/htb/analytics/gatherInfo.png)
![]( /assets/img/htb/analytics/ssh.png)

 Now I can see the user.txt file and I can read it.

## Escalation
 To escalate to root, I continued gathering information and found that this machine has a kernel vulnerbility CVE-2023-2640, which can be exploited for root access.
![]( /assets/img/htb/analytics/gatherInfo2.png)
 Link POC https://www.reddit.com/r/selfhosted/comments/15ecpck/ubuntu_local_privilege_escalation_cve20232640/ 

![]( /assets/img/htb/analytics/exploit2.png)