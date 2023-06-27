---
title: Bypass Reflutter 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: ../assets/img/android/reflutter.png
  alt: image alternative text
---

## Bypass refluter ssl pinning
### Prepare
- Download file apk (vd file digimiBV.apk)
- Download reflutter: 
	**pip3 install reflutter**
- Download uber apk signer ( [uber-apk-signer-1.2.1.jar](https://github.com/patrickfav/uber-apk-signer/releases/download/v1.2.1/uber-apk-signer-1.2.1.jar) )
### Process
- Bypass reflutter
	- Use command: 
    
    ```shell
     reflutter file_appAndroid.apk
    ```
   
	
	![](/assets/img/android/refluterRun.png)
	
	- After using reflutter to bypass the reflutter framework, the APK file still cannot be installed due to the lack of a signature. Therefore, we need to generate a signature for the APK file using uber-apk-signer-1.2.1.jar (details at *Create apk signer*).
	- Configure the port in Burp Suite and the Android device to use port 8083.
	
	![](/assets/img/android/Pasted%20image%2020221121092422.png)
	![](/assets/img/android/Pasted%20image%2020221121092451.png)
	![](/assets/img/android/Pasted%20image%2020221121092506.png)
	
- Create apk signer:

    ```shell
    java -jar uber-apk-signer-1.2.1.jar --apk release.RE.apk
    ```
	![](/assets/img/android/Pasted%20image%2020221121092520.png)

- Install app: 

    ```shell
    adb install release.RE-aligned-debugSigned.apk
    ```
	
	![](/assets/img/android/Pasted%20image%2020221121092544.png)
	
	That's great! You have successfully captured the requests through Burp Suite. Now you can view and analyze the captured requests to continue with security testing or application analysis.
	
	![](/assets/img/android/Pasted%20image%2020221121092559.png)