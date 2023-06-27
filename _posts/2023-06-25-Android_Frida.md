---
title: Bypass SSL & TLS Pinning 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: /assets/img/android/ssl.jpg
  alt: image alternative text
---
## Frida
### Install frida 
- Install frida client on windows or linux
```shell
pip3 install frida-tools
```
- Install frida server on android

```shell
- sử dụng command **adb shell getprop ro.product.cpu.abi** để check version cần download
- download frida server theo link https://github.com/frida/frida/releases/
- giải nén file đã download và đổi tên thành frida-server
- copy file frida-server từ windows lên android bằng adb shell
adb push C:\path\frida-server /data/local/tmp
- Cấp quyền thực thi cho frida-server 
adb shell chmod 755 /data/local/tmp/frida-server
- start frida-server trên android: adb shell /data/local/tmp/frida-server &
    (note phải có ký tự & khi chạy frida-server)
```

## Objection
- Install objection
```shell
pip3 install objection
```
- Start application by objection
```shell
objection -g <package> explore
```
- Disable sslpinning
```shell
android sslpinning disable
```
- Run file js
```shell
frida -U -l file.js -f <package> --no-pause
```
