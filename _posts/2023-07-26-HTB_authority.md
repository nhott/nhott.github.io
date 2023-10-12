---
title: Authority Hack The Box 
date: 2023-07-26 22:50:30 +/-TTTT
categories: [Hack The Box, Authority]
tags: [Authority HTB] 
image:
  path: /assets/img/htb/authority/logo.png
---


## Work Flow
![image]( /assets/img/htb/authority/workflow.png)

## Enumeration
 Use nmap to scan this machine
![image]( /assets/img/htb/authority/nmap.png)


![image]( /assets/img/htb/authority/smbclient.png)


## Exploit

Thực hiện get các folder về và đọc file
![image]( /assets/img/htb/authority/readcode.png)

Thực hiện copy các giá trị ansible_vault và thực hiện crack
![image]( /assets/img/htb/authority/crack.png)
![image]( /assets/img/htb/authority/crack2.png)

Sử dụng thông tin !@#$%^&* crack được sử dụng decrypt ansible vault (link decrypt online https://ansible-vault.braz.dev/ )
![image]( /assets/img/htb/authority/decrypt.png)
![image]( /assets/img/htb/authority/decrypt2.png)
![image]( /assets/img/htb/authority/decrypt3.png)

- Sau khi decrypt ta có 3 thông tin: svc_pwm; pWm_@dm!N_!23; DevT3st@123
- Sử dụng pWm_@dm!N_!2 để thực hiện config manager
![image]( /assets/img/htb/authority/config.png)

Thực hiện edit LDAP connection
![image]( /assets/img/htb/authority/ldap_connect.png)

Trên máy attacker lắng nghe response và click button Test LDAP profile để xem response trả về
![image]( /assets/img/htb/authority/listen.png)
![image]( /assets/img/htb/authority/run.png)
![image]( /assets/img/htb/authority/getaccount.png)
-> Ta đã có được thông tin đăng nhập svc_ldap/ lDaP_1n_th3_cle4r!
![image]( /assets/img/htb/authority/login.png)
![image]( /assets/img/htb/authority/flaguser.png)


## Escalation

- Exploit certs (https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation )
https://systemweakness.com/exploiting-cve-2022-26923-by-abusing-active-directory-certificate-services-adcs-a511023e5366 
- Find vulnerable certificate templates:

![image]( /assets/img/htb/authority/root1.png)
![image]( /assets/img/htb/authority/root2.png)
![image]( /assets/img/htb/authority/root3.png)
![image]( /assets/img/htb/authority/root4.png)
![image]( /assets/img/htb/authority/root5.png)
![image]( /assets/img/htb/authority/root6.png)
![image]( /assets/img/htb/authority/root7.png)

