# Week 3 - Simple Dynamic Analysis




This week we learned about dynamic analysis and how to use tools to conduct simple dynamic analysis.




---

# Lab 3-1 




## Executive Summary




This file does appear to be a piece of malware which VirusTotal labels as a backdoor. It has two main functions, creating a file `C:\windows\system32\vmx32to64.exe` and sending SSL messages to `www.practicalmalwareanalysis.com`. The file created is an exact copy of the executable and has the same MD5 hash and the SSL messages may be a backdoor waiting for commands.




## Indicators of Compromise




**Compilation Date (presumed):** 2008-01-06 14:51:31 UTC




**MD5 Hash:** d537acb8f56a1ce206bc35cf8ff959c0




**File to look for:** `C:\windows\system32\vmx32to64.exe` This file should have the same hash as above.



**Network communication to look for:** Communication with `www.practicalmalwareanalysis.com`: DNS request, TCP connection, and SSL packet sent




## Mitigations




- Scan systems for any file matching the hash and check for `C:\windows\system32\vmx32to64.exe`
- Block `www.practicalmalwareanalysis.com`on the firewall
- Scan the network for SSL packets to `www.practicalmalwareanalysis.com` that are 310 bytes long




## Evidence




Static analysis of this malware reveals a lot of information. VirusTotal detects the file as malware and specifically a backdoor, it also indicates that the malware is packed using PEninja which appears to be a malware packer from a google search. Running `strings` we find some interesting strings "connect %s:%i HTTP /1.0", "www.practicalmalwareanalysis.com", "vmx32to64.exe", and multiple dlls. This means there will most likely be a connection to `www.practicalmalwareanalysis.com`. Looking at dependency walker I only see one dll, `kernel32.dll` being imported despite more being referenced in `strings`, this means it may be packed which is verified by the VirusTotal results. Lastly PEview doesn't reveal anything new but does give us the compilation date.



To set up before running the malware I start a Wireshark capture, start up process explorer, take a regshot, and clear procmon. I then run the executable by double clicking it. A process named Lab03-01.exe pops up pretty much immediately on process explorer. Looking at the dll view I saw many dlls not found by the static analysis done using dependency walker. Some interesting but expected ones are networking dlls like ws2_32.dll. From here I can move to procmon and filter by process name to find what Lab03-01.exe is doing. I see a TCP connection to 10.0.0.21 which is my inetsim machine meaning the suspicion of network activity is correct. Additionally, this processes sets a register `HKLM\software\microsoft\windows\currentversion\run\VideoDriver` to `C:\windows\system32\vmx32to64.exe`. This is confirmed by the regshot comparision. There is also a createfile entry that shows this file `vmx32to64.exe` being created. Checking the file system we find that file and can see that it has the same hash as Lab03-01.exe. This indicates that it copies the executable to this new file and possibly tries to run it by disguising it as the VideoDriver to give it persistance.



The networking activity is logged by both wireshark and inetsim. Looking at wireshark I saw DNS requests for `www.practicalmalwareanalysis.com` followed by a TCP connection and SSL message of 310 bytes each time. It is not possible to tell what the SSL messages do but they are a good network signature to look for and indicate some sort of consistant communication with the website. One possible explaination is that this malware is a backdoor waiting for commands. Inetsim confirms the DNS requests for `www.practicalmalwareanalysis.com` in its logs.




---

# Lab 3-2




## Executive Summary



This file does appear to be a piece of malware which VirusTotal labels as a trojan. It uses process injection to hide in a svchost.exe and access `www.practicalmalwareanalysis.com` where it performs a get request for a serve.html file. It is unclear what this serve.html file is but it most likely contains more malware.




## Indicators of Compromise




**Compilation Date (presumed):** 2010-09-28 01:00:25 UTC



**MD5 Hash:**  84882c9d43e23d63b82004fae74ebb61



**File to look for:** `Lab03-02.dll` and possibly serve.html over the network if a hash is obtained for it



**Network communication to look for:** GET requests for /serve.html to `www.practicalmalwareanalysis.com`




## Mitigations




- Scan systems for any file matching the hash
- Block `www.practicalmalwareanalysis.com`on the firewall
- Scan the network for GET requests to `www.practicalmalwareanalysis.com` for /serve.html




## Evidence




Static analysis of this malware reveals a lot of information. VirusTotal detects the file as malware and specifically a trojan, it also indicates that the malware is not packed. Running `strings` we find a lot interesting strings the most relevant ones being:
- service creation and modification functions
- register creation and manipulation
- network connection and communication
- `practicalmalwareanalysis.com`
- "serve.html"
- a possible service description: "Depends INA+, collects and stores network configuration and location information and notifies application when this information changes"
- `createservice(%s) error %d`
- `%systemroot%\system32\svchost.exe -k netsvcs`
- "IPRIP"
This means there will most likely be a connection to `www.practicalmalwareanalysis.com` possibly looking for serve.html. This malware also likely creates a service which I will use to run it. Looking at dependency walker I see some networking functions imported by ordinal from ws2_32.dll such as connect and send further confirming network activity. Lastly PEview doesn't reveal anything new but does give us the compilation date.



To set up before running the malware I start a Wireshark capture, start up process explorer, take a regshot, and clear procmon. I then run `rundll32.exe Lab03-02.dll, installA` because install seems like a likely function to install the service referenced in the static analysis. This creates a register `HKLM\system\controlset001\services\IPRIP` with value `%systemroot%\system32\svchost.exe -k netsvcs` which tells me IPRIP is probably the service name. Running `net start IPRIP` confirms this. Hovering over various svchost.exe entries in process explorer reveals one with the `%systemroot%\system32\svchost.exe -k netsvcs` command referenced before . Looking at the dll view I saw the Lab03-02.dll is included in this process meaning this is the process Lab03-02.dllo injected into. From here I can move to procmon and filter by process id to find what that process is doing. This unfortunately reveals not hing interesting so I moved on to networking activity.



The networking activity is logged by both wireshark and inetsim. Looking at wireshark I see DNS requests for `practicalmalwareanalysis.com`, a TCP connection to 10.0.0.21 which is the inetsim machine and what DNS returned, and a GET request for /serve.html. It is not possible to tell what the serve.html file does but it is a good network signature to look for. Inetsim confirms the DNS and GET requests for `www.practicalmalwareanalysis.com` in its logs.




---

# Lab 3-3




## Executive Summary



This file does appear to be a piece of malware which performs keylogging. It hides in a svchost.exe process and writes the logged keys to practicalmalwareanalysis.log in the same file as the malware. There is no indication of networking connections to exfiltrate this data in this piece of malware.




## Indicators of Compromise





**Compilation Date (presumed):** 2011-04-08 17:54:23 UTC



**MD5 Hash:**  e2bf42217a67e46433da8b6f4507219e



**File to look for:** `practicalmalwareanalysis.log` However, this file name could change in other versions of this malware and the hash will be constantly changing.






## Mitigations




- Scan for files that match the hash and any files named `practicalmalwareanalysis.log`

- Check for svchost.exe processes with no parent




## Evidence




Static analysis of this malware reveals some information. VirusTotal detects the file as malware and specifically a trojan, it also indicates that the malware is not packed. Running `strings` we find some interesting strings the most relevant ones being process creation and modification functions and file creation and modification functions. There is a reference to svchost.exe which tells me this malware might attempt process injection or something to hide as svchost.exe. There are also a lot of long strings containg repeated 'A' characters it is unclear what those are for. Lastly, there is a reference to "UNICODE LOCALIZATION". Dependency walker doesn't reveal anything else interesting and neither does PEview.



To set up before running the malware I start a Wireshark capture, start up process explorer, take a regshot, and clear procmon. I then double click onteh executable to run it. Process explorer shows a Lab03-03.exe process that runs creates a svchost.exe child process and then abandons it. This child process continues running but doesn't belong to any of the parent processes which looks suspicious and also has a reference to the Lab3 folder in its files on the handles view. From here I can move to procmon and filter by process id to find what that process is doing. The main interesting behavior is the creation and modification of a file called `practicalmalwareanalysis.log`. Navigating to this file in the file explorer and opening it shows the keystrokes entered since the executable was run and confirms that it is a keylogger.



The networking activity is logged by both wireshark and inetsim. For this malware neither of them show any communication attempted by this malware which is expected because it didn't include any networking functions or dlls.



