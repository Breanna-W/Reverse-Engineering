# Week 1 - Simple Static Analysis




This week we learned about static analysis and how to use tools to conduct simple static analysis. We learned how to use VirusTotal to see if the samples are picked up by various antivirus tools. We learned how to use `strings` to find possible null terminated strings in the binaries. We used PEview to view the different sections of the executable. Lastly, we learned how to use dependancy walker to see imported functions by name and by ordinal.




---

# Lab 1-1 




## Executive Summary




The files for this lab appear to be malware specifically a trojan designed to do filesystem manipulation and communicate over the internet. It is unclear what the specific goal of the trojan is but it does create a kerne132.dll file where the l is replaced with a 1. Additionally, it communicates with 127.26.152.13 which the book mentions is an example IP used for the demo but would be an outside device if it was actual malware.




## Indicators of Compromise




**Compilation Date (presumed)(EXE):** 2010/12/19 Sun 16:16:19 UTC



**Compilation Date (presumed)(DLL):** 2010/12/19 Sun 16:16:38 UTC



**MD5 Hash (EXE):** BB7425B82141A1C0F7D60E5106676BB1 




**MD5 Hash (DLL):** 290934C61DE9176AD682FFDD65F0A669  




**File to look for:** `C:\windows\system32\kerne132.dll`



**Network communication to look for:** Communication with 127.26.152.13




## Mitigations




- Scan any Windows machines on the network for `system32\kerne132.dll` or any files with a matching hash

- Block 127.26.152.13 on the firewall




## Evidence




The malware has two parts the executable and a dll. When put into VirusTotal both are detected by many vendors to be trojans.




Using `strings` on the `.EXE`, we find a few interesting strings. The first one 'WARNING_THIS_WILL_DESTROY_YOUR_MACHINE' points to this file being malware but does not provide much information. We also see many file manipulation functions called such as CreateFile, CopyFile, and more. This indicates this malware will attempt some type of file manipulation. Another string `C:\windows\system32\kerne132.dll` indicates that it may use the file manipulation functions to create a kerna132.dll file at that location. 



Using `strings` on the `.DLL`, we find a few other interesting functions particularly CreateProcess and Sleep which could be used to hide in the background and act as a backdoor. Additionally, there is an IP address 127.26.152.13 which the book mentions is used for the example but would be an actual malicious IP in real malware.




PEview reveals that both the executable and dll were compiled at almost the same time on December 19 2010.




Using DependencyWalker on the `.EXE` we don't get any new insights; however the `.DLL` reveals a WS2_32.dll dependancy. The dll imports networking functions such as socket, connect, inet_addr, send, close socket, and more.




---

# Lab 1-2




## Executive Summary



The file for this lab appears to be malware specifically a trojan designed to access a certain website `www.malwareanalysisbook.com` and create a service. It is unclear what this service does however we can check for a service called Malservice and network traffic to the website to identify this malware.




## Indicators of Compromise




**Compilation Date (presumed):** 2011/01/19 Wed 16:10:41 UTC



**MD5 Hash:** 8363436878404DA0AE3E46991E355B83 



**Service to look for:** Malservice



**Network communication to look for:** Communication with `www.malwareanalysisbook.com`




## Mitigations




- Scan any Windows machines on the network for Malservice or any files with a matching hash.

- Block `www.malwareanalysisbook.com` on the firewall




## Evidence




The malware has one part an executable which when put into VirusTotal is detected by many vendors to be a trojan.



Using `strings` on the `.EXE`, we find a few interesting strings. There is what appears to be a website but it is not complete. We also see several interesting functions such as CreateProcess and InternetOpen. Additionally we see a reference to Malservice which is presumably the title of the malicious service




PEview revealed that the EXE was packed using UPX because it had sections labeled UPX0, UPX1, and UPX2. When unpacked it reveals the full website which is `www.malwareanalysisbook.com`.




Dependency Walker didn't reveal any additional information.




---

# Lab 1-3




## Executive Summary




The file for this lab appears to be a trojan, specifically spyware thanks to results from VirusTotal. No further analysis was able to be conducted however since the file is packed using FSG and currently is not able to be unpacked with the tools presented by the book so far.




## Indicators of Compromise





**MD5 Hash:** 9C5C27494C28ED0B14853B346B113145 





## Mitigations




- Scan any Windows machines on the network for any files with a matching hash.




## Evidence



The main indication that this file is malware is the results from VirusTotal since it is packed and is not able to be unpacked currently. Using PEiD we can see it is packed using FSG a packing software with the last stable version from May 24, 2004 (https://en.wikipedia.org/wiki/Executable_compression#Portable_Executable). The packer site has been preserved by the Wayback Machine at https://web.archive.org/web/20040525022811/http://www.xtreeme.prv.pl/ where you see a clearly malicious site advertizing loaders, encryptors, trojans and more. This website appears to still be functional with the last archive being December 10, 2022 and a visit to the website confirms this. The last bit of information we can find on this malware is when it showed up on VirusTotal which is July 4, 2011 and when VirusTotal thinks it showed up first in the wild which is March 26, 2011.



---

# Lab 1-4




## Executive Summary




The file for this lab appears to be malware specifically a trojan designed to download a malicious file from `www.practicalmalwareanalysis.com/updater.exe`. It calls functions to do additional file modification such as creating and writing new files but also moving them. It also attempts to adjust token privledges and access process tokens. Some of the code is hidden in the resource section specifically the URL and download.




## Indicators of Compromise




**Compilation Date (presumed):** 2019/08/30 Fri 22:26:59 UTC



**MD5 Hash:** 625AC05FD47ADC3C63700C3B30DE79AB 



**File to look for:** Modification or creation of `C:\Windows\system32\wupdmgr.exe`



**Network communication to look for:** Communication with `www.practicalmalwareanalysis.com` specifically `www.practicalmalwareanalysis.com/updater.exe`




## Mitigations



- Scan any Windows machines on the network for any files with a matching hash.

- Block `www.practicalmalwareanalysis.com` on the firewall




## Evidence




The malware has one part, an executable, which when put into VirusTotal is detected by many vendors to be a trojan and specifically a downloader. The downloader functionality is supported by evidence from `strings` and PEview.



Using `strings` on the `.EXE`, we find a lot of interesting strings. There are several functions that reveal some of the functionality. CreateFile and WriteFile show that this malware will add files to the file system and write them. MoveFile is also interesting as it indicates this program may attempt to change the existing file system. There is one file refered to with a full path, `C:\Windows\system32\wupdmgr.exe` which upon further research is a Windows XP updater executable so this file may be modified or created if it doesn't already exist. Another interesting file reference is `winlogon.exe` which has no file path so it is unclear if it is refereing to the windows logon executable or creating one with that name. It also uses AdjustTokenPrivledges meaning it will attempt to change its privilege level. `strings` also finds URLDownloadToFile and `www.practicalmalwareanalysis.com/updater.exe` indicating that it will probably try to download updater.exe from that website. The functions LoadResource and WinExec also appear and are interesting considering what is found using PEview.



PEview revealed a hidden program under the .rsrc section as one of the image resources had the structure of a program with its own headers and other sections. This image resource is the one that uses the URLDownloadToFile function which is a common function for downloader malware. It is also probably the one loaded by LoadResource and what WinExec executes.



Dependency Walker verifies all the functions found in `strings` and PEview, however, `strings` shows two dlls Dependency Walker doesn't show. `strings` returns `psapi.dll` and `urlmon.dll` with the `.dll` where the other dlls that do show up in Dependency Walker don't have the `.dll` in  the returned string. This may mean these are being called in a function, possibly LoadLibrary in the code without being linked. It is unclear what these two strings are being used for in this malware.




