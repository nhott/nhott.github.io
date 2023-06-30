---
title: Bypass SSL & TLS Pinning 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: /assets/img/android/ssl.jpg
#   alt: image alternative text
---
## Frida
### Install frida 
- Install frida client on windows or linux
```shell
pip3 install frida-tools
```
- Install frida server on android

```shell
- Check the version of the android device:
     adb shell getprop ro.product.cpu.abi 
- Download frida server https://github.com/frida/frida/releases/
- Extract the downloaded file and rename it to "frida-server"
- Copy frida-server file from windows to android by adb shell:
     adb push C:\path\frida-server /data/local/tmp
- Grant execute permission to the frida-server file:
     adb shell chmod 755 /data/local/tmp/frida-server
- Start frida-server on android device: 
     adb shell /data/local/tmp/frida-server &

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
