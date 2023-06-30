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

## Các kỹ thuật ẩn mình của mã độc
### File có thuộc tính ẩn (hidden file, system file)
- Theo mặc định, windows thiết lập không hiện thị các tệp có thuộc tính ẩn hoặc thuộc tính tệp hệ thống. Do đó mã độc thường lợi dụng cơ chế này để ẩn các file độc hại.
![](/assets/img/malicious/hide_extension.png)

-> Cách để thực hiện điều tra là show hidden file lên
### Giả mạo icon, shortcut, file
#### Giả mạo icon
- Mã độc có thể giả mạo các icon của thư mục, file nén, phần mềm chuẩn, ... để ẩn mình
![](/assets/img/malicious/icon_fake.png)
-> Khi phân tích nên nhìn vào type để nhận biết đó là file thật sự hay là giả dạng
#### Giả mạo shortcut
![](/assets/img/malicious/shortcut_fake.png)
-> Khi phân tích nên xem properties của file để xem shortcut đó trỏ tới đâu
#### Giả mạo file
- Mã độc có thể giả mạo các phần mềm chuẩn bằng cách thay đổi tên của file thực thi chuẩn và file mã độc có tên chuẩn
![](/assets/img/malicious/file_fake.png)
### Side-loading, hijacking dll
- Side-loading: inject đoạn mã độc vào dll khi file thực thi chạy sẽ gọi tới file dll và load mã độc lên
- Hijacking dll: đưa mã độc trực tiếp vào dll (thay thế nội dung file dll), khi file excute chạy sẽ gọi dll và chạy file mã độc dll
![](/assets/img/malicious/hijackingdll.png)
-> Như hình minh họa trên thì file thực thi 1.exe là file chuẩn có chữ ký của adobe, tuy nhiên file dll nằm cùng thư mục là file mã độc, khi 1.exe thực thi sẽ load những dll được thiết lập nhưng mã độc đã đc thay thế nên mã độc sẽ được gọi lên và thực thi

### Fileless
### Pre-OS boot
### Schedule
### Lợi dụng các tiến trình, service hệ thống 
### Virus lây qua file

## Rà soát và xử lý mã độc
### Phát hiện dựa vào các thành phần khởi động
### Phát hiện dựa vào thông tin file, tiến trình
### Phát hiện dựa vào thông tin kết nối mạng
### Phát hiện dựa vào các công cụ kiểm tra mã độc
### Xử lý mã độc

## Biện pháp phòng chống mã độc

## Xử lý sự cố
### Tổ chức cuộc họp
### Ngăn chặn tạm thời

## Thu thập bằng chứng và phân tích điều tra
