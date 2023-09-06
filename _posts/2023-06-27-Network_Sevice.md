---
title: Network Services Pentesting 
date: 2023-06-27 22:50:30 +/-TTTT
categories: [Pentesting, Network Pentesting]
tags: [Services] 
# image: ../assets/img/network/ssh_tunnel.jpg
image:
  path: /assets/img/network/services.jpg
#   alt: image alternative text
---



## Port 21 (FTP service)
- Run Nmap scan with the **ftp-anon** script to find out whether anonymous login is enabled on the machine. Type ****
```shell
nmap -p 21 --script ftp-anon 172.19.19.10
```
- Connect to machine by account anonymous
```shell
    ftp iptarget anonymous
    or
    ftp iptarget 21
```

## Port 22 (SSH service)
- how to connect : ssh user@ip
- ssh key:
    - Case1: Tạo ssh key trên máy attacker (ĐK: attacker đã connect được tới victim)
        - B1: Trên máy attacker:
            - **ssh-keygen** (tạo 2 file *id_rsa* và *id_rsa.pub* lưu tại /home/kali/.ssh )
            - Copy nội dung file *id_rsa.pub* vào vào file /home/username/.ssh/authorized_keys trên máy victim
        - B2: Trên máy victim:
            - thực hiện chép file *id_rsa* vào file *authorized_keys*:
                
                Dùng lệnh: **nano /home/username/.ssh/authorized_keys (paste nội dung copy id_rsa ở B1)**
                
                Hoặc **echo “nội dung id_rsa” > /home/username/.ssh/authorized_keys**
                
            - chmod 600 authorized_keys
        - B3: Trên máy attacker thực hiện ssh account xác thực bằng key:
            
            **ssh -i id_rsa username@ip**
            
    - Case2: Máy victim đã có sẵn file .ssh
        - Trên máy victim thực hiện copy file *id_rsa.pub* vào file *authorized_keys*
            
             **cat id_rsa.pub > /home/username/.ssh/authorized_keys**
            
        - thực hiện copy nội dung của *id_rsa* và *id_rsa.pub* trên máy victim qua file *id_rsa* và *id_rsa.pub* trên máy attacker
        - Trên máy attacker thực hiện ssh bằng key
            
            **chmod 600 id_rsa**
            
            **ssh -i id_rsa username@ip**

## Port 139, 445 (SMB service)
    **nmap -p 139,445 iptarget --script=smb-os-discovery**

**nmap -p 139,445 --script smb-vul* iptarget**

**smbclient -N -L iptarget**  (list những thư mục và user thực hiện share không cần passwd)

**smbclient -N //iptarget/forderShare**  (truy cập forder mục tiêu đc share)

**nmap --script smb-vuln* -p 445 iptarget**  (tìm vuln smb của target)

**nmap --script smb-os-discovery 192.168.0.7** (discover port và infor OS system )

**nmap –sC 192.168.0.7 -d** (Add the “d” option to the command to show the debug trace)

**nmap -p 139,445 $ip --script=smb-os-discovery**

**nmap -p 139,445 --script smb-vul* $ip**

## rpc service and NFS share
    
    **nmap -sP 172.19.19.1-255** ( displays all the hosts that are up in the network)

## Port 79 (Finger service)
```shell
 finger @iptarget  (check username login)
 telnet iptarget 79 (telnet target without paswd)
 telnet username@iptarget  (telnet with passwd)
```
## Port 4555 & 110 (Mail service)
### Port 4555 (Quản trị mail)
    **nc -nC $ipTarget 4555**

    account default root/root

    sử dụng lệnh help để xem các gợi ý như listusers, setpassword, adduser...

### Port 110 (pop3)
    nc -nC $ipTarget 110

## Port 161 (SNMP service)
- Check port open:
    - **sudo nmap -sU --open -p 161 $ip**
    - **sudo nmap -sU --open -p 161 $ip**
- list_community bao gồm:
    - public
    - private
    - manager
- Check all chương trình: **snmpwalk -c public -v1 -t 10 $ip**
    - User Accounts: **snmpwalk -c public -v1 $ip 1.3.6.1.4.1.77.1.2.25**
    - Running Programs: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.25.4.2.1.2**
    - Open TCP ports: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.6.13.1.3**
    - installed Software: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.25.6.3.1.2**
    - System Processes: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.25.1.6.0**
    - Processes Path: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.25.4.2.1.4**
    - Storage Units: **snmpwalk -c public -v1 $ip 1.3.6.1.2.1.25.2.3.1.4**

## Port 111 & 2049 (rpcbind and ntf)
**rpcinfo -p $ip** (check thông tin)

**showmount -e $ip** 

**mkdir folderShare**  (tạo folderShare để mount thông tin từ victim về)

**sudo mount -o nolock $iptarget:/share /folderShare**

**cd folderShare** (xem nội dung được mount về)

## Port 5985, 5986 (evil-winrm)
- brute force
```shell
crackmapexec winrm <IP> -d <Domain Name> -u usernames.txt -p passwords.txt
```
- login
```shell
./evil-winrm.rb -i 172.25.20.6 -u 'username' -p 'passwd’
```

## Port 1433 (Database server mssql)