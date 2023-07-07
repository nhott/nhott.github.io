---
title: Antivirus Evasion 
date: 2023-06-25 22:50:30 +/-TTTT
categories: [Malicious,Antivirus Evasion]
tags: [Antivirus Evasion] 

image:
  path: /assets/img/malicious/yara.png
#   alt: image alternative text
---
### **Detection Methods**

- Signature-based antivirus detection is mostly considered a blacklist technology 
- filesystem is scanned for known malware signatures and any are detected, the offending file are quarantined

---> we can bypass signature-based detected by simply changing or obfuscating the contents of a  known malicious file int order to break the identifying byte sequence (signature)
---> sometimes we can bypass antivirus software by simply changing a couple of harmless strings inside the binary file from uppercase to lowercase

Have 2 methods to detect malicious
- Heuristic-Based Detection is a detection method that relies on various rules and algorithms to determine whether or not an action is considered malicious ( analyze the source code)
- Behavior-Based Detection dynamically analyzes the behavior of a binary file

### **Bypass Antivirus Detection**
 Antivirus evasion have 2 broad categories: on-disk and in-memory

#### **On-Disk Evasion**
 Various techniques used to obfuscate files stored on a physical disk

##### *Packers*
- One of the earliest ways of avoiding detection involved the use of packers.
-  packers were designed to simply reduce the size of an executable

##### *Obfuscators*
- Obfuscators reorganize and mutate code in a way that makes it more difficult to reverse-engineer. This includes replacing instructions with semantically equivalent ones, inserting irrelevant instructions or "dead code" splitting or reordering functions

##### *Crypters*
"Crypter" software cryptographically alters executable code, adding a decrypting stub that restores the original code upon execution. This decryption happens in-memory, leaving only the encrypted code on-disk. Encryption has become foundational in modern malware as one of the most effective AV evasion techniques.
##### *Software Protectors*
- Highly effective antivirus evasion requires a combination of all of the previous techniques in addition to other advanced ones, including anti-reversing, anti-debugging, virtual machine emulation detection, and so on. In most cases, software protectors were designed for legitimate purposes but can also be used to bypass AV detection.
- *Enigma Protector* can successfully be used to bypass antivirus products

#### **In-memory evasion**
 In-Memory Injections also known as *PE Injection* is a popular technique used to bypass antivirus products. Rather than obfuscating a malicious binary, creating new sections, or changing existing permissions, this technique instead focuses on the manipulation of volatile memory. One of the main benefits of this technique is that it does not write any files to disk, which is one the main areas of focus for most antivirus products.

##### *Remote Process Memory Injection*
- This technique attempts to inject the payload into another valid PE that is not malicious. The most common method of doing this is by leveraging a set of Windows APIs. First, we would use the _OpenProcess function to obtain a valid _HANDLE to a target process that we have permissions to access. After obtaining the HANDLE, we would allocate memory in the context of that process by calling a Windows API such as _VirtualAllocEx_. Once the memory has been allocated in the remote process, we would copy the malicious payload to the newly allocated memory using _WriteProcessMemory_. After the payload has been successfully copied, it is usually executed in memory in a separate thread using the _CreateRemoteThread API.

- This sounds complex, but we will use a similar technique in the following example, using PowerShell to do most of the heavy lifting and perform a very similar but simplified attack targeting a local powershell.exe instance.
##### *Reflective DLL Injection*
- Unlike regular DLL injection, which implies loading a malicious DLL from disk using the _LoadLibrary API, this technique attempts to load a DLL stored by the attacker in the process memory.

- The main challenge of implementing this technique is that _LoadLibrary_ does not support loading a DLL from memory. Furthermore, the Windows operating system does not expose any APIs that can handle this either. Attackers who choose to use this technique must write their own version of the API that does not rely on a disk-based DLL.
##### *Process Hollowing*
- When using process hollowing to bypass antivirus software, attackers first launch a non-malicious process in a suspended state. Once launched, the image of the process is removed from memory and replaced with a malicious executable image. Finally, the process is then resumed and malicious code is executed instead of the legitimate process.
##### *Inline hooking*
 - this technique involves modifying memory and introducing a hook (instructions that redirect the code execution) into a function to point the execution flow to our malicious code. Upon executing our malicious code, the flow will return back to the modified function and resume execution, appearing as if only the original code had executed.