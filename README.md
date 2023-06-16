# CS479/579 Reverse Engineering at NMSU

## Summary

This GitHub repository contains reports on the labs conducted during the Spring 2023 CS 579 course at NMSU. The labs consist of various reverse engineering activities conducted on malware samples from "Practical Malware Analysis"

## System Setup

### Setup
The malware samples are all windows malware so a windows virtual machine was set up to examine the malware. This visual machine was set up using virtual box on a MacBook and had all network adapters turned off so the machine would be isolated from the rest of the network. 

### Isolation
Isolation is necessary because working with malware is dangerous as it could spread and infect other machines. The network adapters are turned off to prevent it from communicating across the network and the virtual machine is run on Mac OS so that if it does escape on to the host computer there is less of a chance for the malware to work on the host computer.

### Tools Installed
* VirusTotal
* strings (Microsoft Sysinternals)
* PEview
* Dependency Walker
* PEiD
* process explorer
* regshot
* procmon
* inetsim
* Wireshark
* Ghidra
* ltrace
* gdb
* uftrace
* Python pwntools
