---
title: Phân tích Mã Độc 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Malicious, Analysic Malicious]
tags: [malicious] 
# image: ../assets/img/network/ssh_tunnel.jpg
image:
  path: /assets/img/malicious/logo.png
#   alt: image alternative text
---

## **Các kỹ thuật ẩn mình của mã độc**
### File có thuộc tính ẩn (hidden file, system file)
- Theo mặc định, windows thiết lập không hiện thị các tệp có thuộc tính ẩn hoặc thuộc tính tệp hệ thống. Do đó mã độc thường lợi dụng cơ chế này để ẩn các file độc hại.
![image](/assets/img/malicious/hide_extension.png)

-> Cách để thực hiện điều tra là show hidden file lên
### Giả mạo icon, shortcut, file
#### Giả mạo icon
- Mã độc có thể giả mạo các icon của thư mục, file nén, phần mềm chuẩn, ... để ẩn mình
![image](/assets/img/malicious/icon_fake.png)
-> Khi phân tích nên nhìn vào type để nhận biết đó là file thật sự hay là giả dạng
#### Giả mạo shortcut
![image](/assets/img/malicious/shortcut_fake.png)
-> Khi phân tích nên xem properties của file để xem shortcut đó trỏ tới đâu
#### Giả mạo file
- Mã độc có thể giả mạo các phần mềm chuẩn bằng cách thay đổi tên của file thực thi chuẩn và file mã độc có tên chuẩn
![image](/assets/img/malicious/file_fake.png)
### Side-loading, hijacking dll
- Side-loading: inject đoạn mã độc vào dll khi file thực thi chạy sẽ gọi tới file dll và load mã độc lên
- Hijacking dll: đưa mã độc trực tiếp vào dll (thay thế nội dung file dll), khi file excute chạy sẽ gọi dll và chạy file mã độc dll
![image](/assets/img/malicious/hijackingdll.png)
-> Như hình minh họa trên thì file thực thi 1.exe là file chuẩn có chữ ký của adobe, tuy nhiên file dll nằm cùng thư mục là file mã độc, khi 1.exe thực thi sẽ load những dll được thiết lập nhưng mã độc đã đc thay thế nên mã độc sẽ được gọi lên và thực thi

### Fileless
Fileless Malware là phần mềm hoạt động không tồn tại dưới dạng file mà nó chỉ là những command line
![image](/assets/img/malicious/fileless.png)

### Pre-OS boot
 Mã dộc có thể ghi đè MBR (Master Boot Record) để ẩn mình.
### Schedule
 Chương trình mã độc chỉ chạy trong điều kiện thời gian nhất định được thiết lập sẵn để tránh sự phát hiện.
![image](/assets/img/malicious/schedule.png)
### Lợi dụng các tiến trình, service hệ thống 
![image](/assets/img/malicious/service.png)
![image](/assets/img/malicious/service2.png)

### Virus lây qua file
  Mã độc ẩn minhfbawngf cách lấy nhiễm vào các chương trình, tệp thực thi.
![image](/assets/img/malicious/virus.png)

## **Rà soát và xử lý mã độc**
### Phát hiện dựa vào các thành phần khởi động
 Thực hiện kiểm tra:
 + Các key startup trong registry
 + Các file trong thư mục startup
 + Các file có đăng ký task scheduler
 + File autorun.inf

### Phát hiện dựa vào thông tin file, tiến trình
- Kiểm tra tại thư mục AppData, file mã độc thường được ẩn ở path là AppData.
- Kiểm tra tên các tiến trình, trong hệ thống các tên thường đặt theo chức năng không đặt tên vô nghĩa.
- Kiểm tra tên phần mềm, phần mềm chuẩn sẽ có đầy đủ thông tin liên quan.
- Kiểm ta tên tiến trình, đường dẫn của tiến trình.
- Kiểm tra thông tin tiến trình cha, tiến trình con, thời gian tạo và các tham số đầu vào.
- Kiểm tra thông số %CPU, tốc độ dọc ghi ổ đĩa, các luông hoạt động, stack, các thư viện được sử dụng.
- Kiểm tra sự tác động cảu tiến trình tới hệ thống.

### Phát hiện dựa vào thông tin kết nối mạng
- Dựa vào thông tin Domain, IP, port kết nối đến và đi.
- Dựa vào lưu lượng mạng, dữ liệu gửi nhận.
- Dựa vào tần suất và tiến trình kết nối.

### Phát hiện dựa vào các công cụ kiểm tra mã độc
 Một số công cụ phổ biến như virustotal, sandbox, anyrun, cuckoo cho phép chúng ta có thể quét file, domain, IP, URL và tìm kiếm theo mã hash để phát hiện những mã đọc đã được phát hiện.
- Ngoài ra còn một số công cụ rà soát sau:

| Đối tượng | Tools                                          |
| --------- | ---------------------------------------------- |
| File      | Explorer, cmd                                  |
| Process   | Task manager, Process Explorer, Process Hacker |
| Network   | TcpView, Wireshark                             |
| Startup   | Regedit, AutoRuns                              |
| Rootkit   | PC Hunter                                      |
| Logs      | Event Viewer, Process Monitor                  |


### Xử lý mã độc
- Thiết lập tùy chọn hiện file ẩn, hiện phần mở rộng của file.
- Sử dụng các công cụ hỗ trợ xử lý.
- Khôi phục lại dữ liệu gốc trước khi xử lý mã độc trên USB.
- Kill process của mã độc.
- Xóa file và các thành phần khởi động.
- Khôi phục các cấu hình quan trọng của máy.
- Restart lại máy xem còn dấu hiện của mã độc không.


## Biện pháp phòng chống mã độc
- Luôn cập nhật phần mềm ở phiên bản mới nhất.
- Các doanh nghiệp cần áp dụng phân chia hệ thống mạng, phân quyền truy cập cho nhân viên.
- Trang bị các phần mềm Antivirus có bản quyền.
- Trang bị Firewall, các lớp giám sát hệ thống.
- Không sử dụng các phần mềm crack.
- Không mở các file đính kèm không rõ nguồn gốc.
- Đặt mật khẩu cho các tài khoản, windows đảm bảo đủ mạnh.

