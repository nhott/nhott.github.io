---
title: Introduction Android Applications Pentesting 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: ../assets/img/android.jpg
  alt: image alternative text
---



# Setup
## Support download file apk
### Download file apk online
 [https://apkpure.com/es/](https://apkpure.com/es/),
 [https://m.apkpure.com/vn/](https://m.apkpure.com/vn/)
 [https://www.apkmirror.com/](https://www.apkmirror.com)
 [https://apkcombo.com/es-es/apk-downloader/](https://apkcombo.com/es-es/apk-downloader/)
 ### Extract file apk from android device
 ```shell
 - Show the packages on device:
	 **adb shell pm list packages** (Ex: The package name com.android.insecurebankv2) 

- Show list the path to file: 
	**adb shell pm path com.android.insecurebankv2**
package:/data/app/com.android.insecurebankv2/base.apk

- get file to machine by pull 
	**adb pull /data/app/com.android.insecurebankv2/base.apk**
```

## Thực hiện add cert burpsuite vào system cert android
```shell
/data/misc/user/0/cacerts-added -> thấy cái 9a5.....0 

mount -o rw,remount /system

mv 9a5...0 /system/etc/security/cacerts/

chmod 644 /system/etc/security/cacerts/9a5...0

mount -o ro,remount /system
```
## How to root android devices
### Use software
You can use genymotion, Menuplay 
### Use tool
 - Root Certificate Manager (https://m.apkpure.com/vn/root-certificate-manager-root/net.jolivier.cert.Importer)
 - 3C All in One toolbox (https://m.apkpure.com/vn/3c-all-in-one-toolbox/ccc71.at.free)
 - Magisk Manager (https://magiskmanager.com/)
## Config proxy to get request on burp suite
Có 3 cách để cấu hình proxy: cấu hình manual, cấu hình qua app, cấu hình bằng lệnh adb
### cofigure manual
Thực hiện cấu hình proxy manual trên wifi và configure address và port tương tự trên burp suite

![](/assets/img/android/proxy_burp.png)

Thử truy cập trình duyệt trên thiết bị android để xem burpsuite đã bắt được request
Open browser on android device to check

![](/assets/img/android/access_browser.png)

### configure by app
install app ProxyDroid trên thiết bị android (https://play.google.com/store/apps/details?id=org.proxydroid&hl=vi&gl=US)
### set wifi proxy via adb shell
- câu lệnh set proxy
```shell
adb.exe shell settings put global http_proxy <address>:<port>
```
- remove proxy 
``` shell
adb.exe shell settings put global http_proxy :0
```
https://android.stackexchange.com/questions/98287/how-to-set-wifi-proxy-via-adb-shell

## bypass by app 
https://m.apkpure.com/vn/root-certificate-manager-root/net.jolivier.cert.Importer

