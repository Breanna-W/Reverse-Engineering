# Week 3 - Simple Dynamic Analysis




This week we learned about dynamic analysis and how to use tools to conduct simple dynamic analysis.




---

# Lab 3-1 




## Executive Summary




This file does appear to be a piece of malware which VirusTotal labels as a backdoor. It has two main functions, creating a file `C:\windows\system32\vmx32to64.exe` and sending SSL messages to `www.practicalmalwareanalysis.com`. The file created is an exact copy of the executable and has the same MD5 hash and the SSL messages may be a backdoor waiting for commands.




## Indicators of Compromise




**Compilation Date (presumed)(EXE):** 2008-01-06 14:51:31 UTC




**MD5 Hash (EXE):** d537acb8f56a1ce206bc35cf8ff959c0




**File to look for:** `C:\windows\system32\vmx32to64.exe` This file should have the same hash as above.



**Network communication to look for:** Communication with `www.practicalmalwareanalysis.com`: DNS request, TCP connection, and SSL packet sent




## Mitigations




- Scan systems for any file matching the hash and check for `C:\windows\system32\vmx32to64.exe`
- Block `www.practicalmalwareanalysis.com`on the firewall
- Scan the network for SSL packets to `www.practicalmalwareanalysis.com` that are 310 bytes long




## Evidence




Static analysis of this malware reveals a lot of information. VirusTotal detects the file as malware and specifically a backdoor, it also indicates that the malware is packed using PEninja which appears to be a malware packer from a google search. Running `strings` we find some interesting strings "connect %s:%i HTTP /1.0", "www.practicalmalwareanalysis.com", "vmx32to64.exe", and multiple dlls. This means there will most likely be a connection to `www.practicalmalwareanalysis.com`. Looking at dependency walker I only see one dll, `kernel32.dll` being imported despite more being referenced in `strings`, this means it may be packed which is verified by the VirusTotal results. Lastly PEview doesn't reveal anything new but does give us the compilation date.




After this I start the dynamic analysis. The sandbox reveals



To set up before running the malware I start a Wireshark capture, start up process explorer, take a regshot, and clear procmon. I then run the executable by double clicking it. A process named Lab03-01.exe pops up pretty much immediately on process explorer. Looking at the dll view we see many dlls not found by the static analysis done using dependency walker. Some interesting but expected ones are networking dlls like ws2_32.dll. From here I can move to procmon and filter by process name to find what Lab03-01.exe is doing. I see a TCP connection to 10.0.0.21 which is my inetsim machine meaning the suspicion of network activity is correct. Additionally, this processes sets a register `HKLM\software\microsoft\windows\currentversion\run\VideoDriver` to `C:\windows\system32\vmx32to64.exe`. This is confirmed by the regshot comparision. There is also a createfile entry that shows this file `vmx32to64.exe` being created. Checking the file system we find that file and can see that it has the same hash as Lab03-01.exe. This indicates that it copies the executable to this new file and possibly tries to run it by disguising it as the VideoDriver to give it persistance.



The networking activity is logged by both wireshark and inetsim. Looking at wireshark we see DNS requests for `www.practicalmalwareanalysis.com` followed by a TCP connection and SSL message of 310 bytes each time. It is not possible to tell what the SSL messages do but they are a good network signature to look for and indicate some sort of consistant communication with the website. One possible explaination is that this malware is a backdoor waiting for commands. Inetsim confirms the DNS requests for `www.practicalmalwareanalysis.com` in its logs.




---

# Lab 3-2




## Executive Summary








## Indicators of Compromise




**Compilation Date (presumed):** 



**MD5 Hash:**  



**File to look for:** 



**Network communication to look for:** 




## Mitigations




- 

- 




## Evidence









---

# Lab 3-3




## Executive Summary








## Indicators of Compromise





**Compilation Date (presumed):** 



**MD5 Hash:**  



**File to look for:** 



**Network communication to look for:** 





## Mitigations




- 

-  




## Evidence








