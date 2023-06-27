---
title: Findding The Vulnerability 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Pentesting, Android Applications]
tags: [Android Pentesting] 

image:
  path: /assets/img/android/findding.jpg
  alt: image alternative text
---

# Finding Vulnerability
## Reverse engineer an APK file
- You can use tools like APKTool, JADX, JD-GUI, or JADX-GUI to extract its contents. These tools will decompile the APK file and provide you with the source code and other resources.
- Any reversed APK file, a new signature needs to be generated in order to install and run it.

### Use APKTool to reverse an APK file

- Unpackage (decompile)
```shell
apktool d <source.apk> -o <destination_directory>/
```

- Recompile

```shell
apktool b <source_directory>/ -o <patched.apk>
```

- Create a signing key by using keytool:
```shell
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

- Sign the resulting apk with jarsigner
```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore <patched.apk> alias_name
```

- Install the application on the device
```shell
adb install <patched.apk>
```

## Automated vulnerability scanning using tools
- Check DoS: **AnDOSid**
- Find and exploit android app vulnerabilities: **Drozer**
- Conduct vulnerability scanning: **zANTI**
- Perform network assessments on wireless network: **dSploit**

## Intent Sniffing

- Android application uses **intents** for inter-application and intra-application communication. Two types of Intents are used by an application to communicate with other application: **Explicit Intent** and **implicit Intent**. Explicit Intent is secure as it communicates inside the application, but ***implicit Intent is not***. So, if your application is using implicit Intent, hackers may sniff, modify, steal, or replace the content of Intent to compromise user privacy and application security. 

- Check the types of intents used by the application. If it uses implicit intents, the application may be susceptible to sniffing
- Tool: 
	- Intent sniffer: It monitors runtime routed broadcasts Intents, sent between applications on the system. 
	- Intent fuzzer: finds bugs that cause the system to crash, as well as performance issues on devices, applications, or custom platform distributions

## Insecure logging
- It is displaying trigger information on the screen
-  Check logcat
In the AndroidManifest.xml, search for logActivity to identify the classes and proceed to those classes to check for LogActivity.

## Hardcoding
Check for hardcoded values in the AndroidManifest.xml file, and if found, examine the classes containing hardcoded values to gather relevant information.

## Check for sensitive data 
Use adb to find the sensitive data:
	logcat
	cd /mnt/sdcard

## Tapjacking
Tapjacking is an attack where a malicious application is launched and positions itself on top of a victim application. Once it visibly obscures the victim app, its user interface is designed in such a way as to trick the user to interact with it, while it is passing the interaction along to the victim app. In effect, it is blinding the user from knowing they are actually performing actions on the victim app.

In order to detect apps vulnerable to this attacked you should search for exported activities in the android manifest (note that an activity with an intent-filter is automatically exported by default). Once you have found the exported activities, check if they require any permission. This is because the malicious application will need that permission also. Finally, it's important to check the code for possible `setFilterTouchesWhenObscured` configurations. If set to `true`, a button can be automatically disabled if it is obscured:
```shell
<Button android:text="Button"

android:id="@+id/button1"

android:layout_width="wrap_content"

android:layout_height="wrap_content"

android:filterTouchesWhenObscured=** "true"**>

</Button>
```
You can use [**qark**](https://github.com/linkedin/qark) with the `--exploit-apk` parameter to create a malicious application to test for possible Tapjacking vulnerabilities.
Link qark manual: https://github.com/linkedin/qark/releases

## Insecure data storage
Reference source https://manifestsecurity.com/android-application-security-part-8/
Check the data at: Shared Preferences, Database, Temp Files, External storage
### Internal storage
-  Files **created** on **internal** storage are **accessible** only by the **app**. This protection is implemented by Android and is sufficient for most applications. But developers often use `MODE_WORLD_READBALE` & `MODE_WORLD_WRITABLE` to give access to those files to a different application, but this doesn’t limit other apps(malicious) from accessing them.
- How to detect vuln:
	- Search keyword **MODE_WORLD_WRITEABLE or MODE_WORLD_READABLE**
	- Check the tmp files stored in /data/data/<packeges> 
	- Check the information stored in /data/data/<packeges>/shared_prefs/<file>
	- Check the hardcode
	- Check the database file in /data/data/<packeges>/databases 

	Check db by sqlite3:
	 sqlite3 file_db  ->  .tables  -> select * from name_table
- How to fix:
	- Do not use `MODE_WORLD_WRITEABLE` or `MODE_WORLD_READABLE` modes for IPC files
	- Avoid exclusively relying upon hardcoded encryption or decryption keys when storing sensitive information assets because those keys can be retrived after decompiling the app
	- Consider providing an additional layer of encryption beyond any default encryption mechanisms provided by the operating system.

### External storage
- Files created on external storage, such as SD Cards, are globally readable and writable. Because external storage can be removed by the user and also modified by any application, you should not store sensitive information using external storage.

- Check vuln:
	- External storage can be accessed in /storage/emulated/0 , /sdcard , /mnt/sdcard
	- Sensitive data stored in clear-text:
		- **Shared preferences**: Android allow to each application to easily save xml files in the path `/data/data/<packagename>/shared_prefs/` and sometimes it's possible to find sensitive information in clear-text in that folder.
		- **Databases**: Android allow to each application to easily save sqlite databases in the path `/data/data/<packagename>/databases/` and sometimes it's possible to find sensitive information in clear-text in that folder.
	- Use Drozer tool

## Static analysic

### Weak or improper cryptography use:
- Incorrect uses of encryption algorithm may result in sensitive data exposure, key leakage, broken authentication, insecure session and spoofing attack.
- Find keyword such as **SecretKeySpec, aes, iv**
	
### Exported preference Activities
- Android's activity component is application screen(s) and the action(s) that applied on that screen(s) when we use the application. When as activity is found to be shared with other apps on the device therefore leaving it accessible to any other application on the device.
- Find key word: **exported="true"**
	
### Apps which enable backups
- This is considered a security issue because people could backup your app via ADB and then get private data of your app into their PC
- Find key word: **allowBackup="true"**

### Apps which are debuggable
- Debugging was enabled on the app which makes it easier for reverse engineers to hook a debugger to it. This allows dumping a stack trace and accessing debugging helper classes.
- Find key word: **debuggable="true"**

### App permissions
- System permissions are divided into two groups: “normal” and “dangerous.” Normal permission groups are allowed by default, because they don’t pose a risk to your privacy. (e.g., Android allows apps to access the Internet without your permission.) Dangerous permission groups, however, can give apps access to things like your calling history, private messages, location, camera, microphone, and more. Therefore, Android will always ask you to approve dangerous permissions.
- Find key word: **uses-permission**

### Firebase Instance
- Google's Firebase service is one of the most popular back-end development platforms for mobile and web applications that offers developers a cloud-based database, which stores data in JSON format and synced it in the real-time with all connected clients
-  Identify misconfigured firebase instances by firebase scanner: https://github.com/shivsahni/FireBaseScanner 
- How to use FirebaseScanner: **python FireBaseScanner.py -p /path/apk**

### Sensitive data in the code
-  Find the Users, passwords, internal IP and more ...

## Automation analysic
- Mara framework
		./mara.sh -s 'file.apk'
- QARK
- MobSF