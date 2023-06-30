---
title: Prepare For an Android Penetration Testing
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: /assets/img/android.jpg
#   alt: image alternative text
---


## Download file apk
### Download file apk from online

 [https://apkpure.com/es/](https://apkpure.com/es/)

 [https://m.apkpure.com/vn/](https://m.apkpure.com/vn/)

 [https://www.apkmirror.com/](https://www.apkmirror.com)

 [https://apkcombo.com/es-es/apk-downloader/](https://apkcombo.com/es-es/apk-downloader/)

### Extract file apk from android device
 ```shell
 - Show the packages on device:
	 adb shell pm list packages (Ex: The package name com.android.insecurebankv2) 

- Show list the path to file: 
	adb shell pm path com.android.insecurebankv2
-> package:/data/app/com.android.insecurebankv2/base.apk

- Get file to machine by pull 
	adb pull /data/app/com.android.insecurebankv2/base.apk
```

## Add burp's certificate to the Android system
```shell
/data/misc/user/0/cacerts-added -> you are seeing a file named 9a5.....0 

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
There are three ways to configure a proxy: manual configuration, configuration through an app, and configuration using adb commands.

### Manual Configuration:
Configure the IP address and port on both Wi-Fi and Burp Suite to be the same:
- Open the settings on your Android device.
- Navigate to the Wi-Fi or Network settings.
- Find and select the network you're connected to.
- Look for the Proxy settings option.
- Enter the proxy server address and port number provided by your network administrator or proxy service.

![](/assets/img/android/proxy_burp.png)

Check the Burp Suite is capturing requests from the browser on your Android device

![](/assets/img/android/access_browser.png)

### Configuration through an App:
- Install a proxy app from the Google Play Store, such as ProxyDroid or Orbot.
- Open the proxy app and follow the on-screen instructions to configure the proxy settings.
- Typically, you will need to provide the proxy server address, port number, and any authentication credentials if required.

### Configuration using adb (Android Debug Bridge):
- Set proxy
```shell
adb.exe shell settings put global http_proxy <address>:<port>
```
- Remove proxy 
``` shell
adb.exe shell settings put global http_proxy :0
```

### Use iptables to intercept traffic through Burp Suite on an Android device
In some cases, when testing in a UAT environment with a specific port (e.g., port 8765), it is necessary to set up iptables to capture connections. Here are the steps to set up iptables on an Android device:
```shell
iptables -t nat -A OUTPUT -p tcp --dport 80 -j DNAT --to-destination 192.168.63.1:8083

iptables -t nat -A OUTPUT -p tcp --dport 443 -j DNAT --to-destination 192.168.63.1:8083

iptables -t nat -A OUTPUT -p tcp --dport 8765 -j DNAT --to-destination 192.168.63.1:8083

iptables -t nat -A POSTROUTING -p tcp --dport 80 -j MASQUERADE

iptables -t nat -A POSTROUTING -p tcp --dport 443 -j MASQUERADE

iptables -t nat -A POSTROUTING -p tcp --dport 8765 -j MASQUERADE
```


