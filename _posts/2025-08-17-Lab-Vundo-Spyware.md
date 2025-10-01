---
title: "Dissecting the Vundo Trojan in a Windows XP Lab"
date: 2025-08-17
categories: [Malware Analysis, Labs]
tags: [trojan, vundo, spyware, wireshark, windowsxp]
layout: post
---

# Dissecting the Vundo Trojan in a Windows XP Lab  

In this lab, I infected a Windows XP virtual machine with the **Vundo Trojan** to observe how it behaves on both the host system and the network. Using **Wireshark** for traffic capture and **Malwarebytes** for detection, I was able to follow the infection chain from initial execution to persistence and attempted communication with attacker servers.  

This post documents what happened step by step.  

---

## Normal Windows XP Traffic  

Before infection, I captured baseline traffic on a clean XP VM. Even when idle, Windows XP generates background network activity, such as:  

- **NetBIOS Name Service (NBNS)** → broadcasts to discover other machines.  
- **ARP** → resolving IP addresses into MAC addresses.  
- **Browser Service** → maintaining the local workgroup list.  

![Wireshark Capture](assets/img/Lab_VUNDO/wireshark_icmp.png)  

This traffic is normal and expected for XP systems in a LAN environment.  

---

## Infection Phase  

First, I browsed a safe website to compare normal browsing traffic.  

![Wireshark Capture](assets/img/Lab_VUNDO/wireshark_icmp_website_normal.png)  

Then I accessed a **malicious site**:  

http://www.server.com/nasty

From there, a file named `1001Passwords.exe` was downloaded. This executable acted as the **dropper**:  

- It placed malicious DLL files into `C:\WINDOWS\system32`.  
- Example DLLs included:  
  - `wintug32.dll`  
  - `jkkiff.dll`  

![System32](assets/img/Lab_VUNDO/System32_scn.png)  

The EXE itself wasn’t the main threat here the DLLs were the actual Vundo payload.  

---

## Network Behavior After Infection  

Once the Trojan was active, Wireshark showed suspicious traffic. The system repeatedly attempted to resolve strange domains, including:  

- SEARCHMEUP.BIZ
- HOMESCRAP.BIZ
- NAVCANCLPUEF.HKCOO
- NAVCANZVLUPF.HKCOO


![WireShark Capture](assets/img/Lab_VUNDO/Suspicious_cap_wireshark.png)  

Because the lab environment was isolated, DNS lookups failed. The malware then attempted **NBNS** and **ARP queries** on the LAN, essentially broadcasting:  

> *“Does anyone know where SEARCHMEUP.BIZ is?”*  

When no response was received, repeated **ICMP Destination Unreachable** messages were generated. This shows how malware adapts — if DNS isn’t working, it tries alternative methods to locate its Command & Control (C2) servers.  

---

## Detection and Removal  

The malicious DLLs were difficult to delete manually because:  

- They were loaded into memory.
- Registry entries made sure that they reloaded on startup.  

![Deleting DLL Files](assets/img/Lab_VUNDO/Deleting_DLL_FILES.png)  

I ran **Malwarebytes Anti-Malware** inside the VM:  

- It detected the infected DLLs and registry entries.  
- Warned that files could not be removed immediately.  
- After reboot and cleaning, the system was restored.  

![Malwarebytes](assets/img/Lab_VUNDO/Malwarebytes_scan.png)  

---

## Real-World Context  

Although this was a controlled lab, the infection process mirrors how users get infected in the real world. A common scenario is downloading cracked software, “keygens,” or fake installers from suspicious sites.  

- The **crack or keygen EXE** often acts as the dropper.  
- It installs hidden DLLs or executables in system directories.  
- These payloads contact attacker-controlled domains (C2 servers) to get instructions, steal data, or deliver additional malware.  

This is why cracked software is such a common method for Trojans — people willingly execute the EXE, bypassing most security warnings.  

---

## Key Takeaways  

1. **Droppers vs. Payloads** → The EXE was just the installer; the DLLs were the real threat.  
2. **Persistence** → Vundo used the registry and `windows system proccess to reload after reboot.  
3. **C2 Attempts** → The malware tried multiple protocols (DNS, NBNS, ARP) to reach its servers.  
4. **Removal Challenges** → Files locked in memory required reboot and anti-malware tools to clean.  
5. **Real-World Risk** → Cracks and keygens are one of the most common ways attackers deliver Trojans like Vundo.  

---

## Final Thoughts  

This lab gave me a hands-on view of how a Trojan works from infection to attempted communication. 

It also reinforced a key lesson:
 **if you download and run cracked software, you’re essentially giving attackers an open door into your system.**

 ---

 ~ Rex