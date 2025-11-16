---
title: "Metasploitable 2 — Full Port Scan & Vulnerability Mapping"
date: 2025-11-16
categories: [Pentesting, Metasploitable]
tags: [nmap, enumeration, recon, vulnerability, lab]
layout: post
---

# Metasploitable 2 — Full Port Scan & Vulnerability Mapping

After verifying the target machine was alive on the network, the next step was to perform a comprehensive enumeration of all exposed services. This type of scanning is foundational in any penetration test because it defines the attack surface and reveals which services may be vulnerable.

For this stage, I used Nmap to conduct a full port scan along with service and version detection. Metasploitable 2 intentionally exposes a large number of insecure services, so this scan produced a broad dataset for further exploitation.

---

## Full Nmap Service Scan

To identify all open ports and detect running services, I used the following command:

```bash
nmap -sV -A -p- 192.168.50.63 
OR
nmap -T 5 -A -p- 192.168.50.63
```

Where:

- `-p-` scans all 65,535 TCP ports  
- `-sV` enables service version detection  
- `-A` enables OS detection, script scanning, and traceroute  

Nmap returned a large number of open services. Below is the captured scan output:

![Full Nmap Scan](/assets/img/metasploitable2/fig3_nmap_fullscan.png)

Download full Nmap scan:  
[Full Scan Output](/assets/nmap_metasploitable/nmap_fullscan.txt)

---

# Port-by-Port Vulnerability Breakdown

Below is a breakdown of the key services identified, along with typical vulnerabilities associated with each one.

---

## Remote Access Services

### 21/tcp — FTP (vsftpd 2.3.4)
- Allows anonymous login  
- Contains a known backdoor that triggers a malicious shell on port 6200  
- This vulnerability is documented as CVE-2011-2523  

This service provides a reliable entry point into the system.

---

### 22/tcp — SSH (OpenSSH 4.7p1 Debian)
- Very outdated version  
- Commonly configured with weak or default credentials  
- Often exploited after discovering valid usernames via other services  

Practical for brute-force attacks.

---

### 23/tcp — Telnet
- Provides unencrypted remote shell access  
- Susceptible to password guessing  
- Credentials transmitted in cleartext

Rarely used today, but always dangerous when exposed publicly.

---

### 1524/tcp — IngresLock / Bindshell
This is an intentional backdoor included in Metasploitable for training purposes, providing a pre-spawned root shell.

---

## Email Service

### 25/tcp — SMTP (Postfix)
Nmap enumeration showed several enabled capabilities including:

- VRFY  
- PIPELINING  
- ENHANCEDSTATUSCODES  
- STARTTLS  

The most significant is the **VRFY** command, which allows attackers to confirm the existence of system users.

![SMTP Scan](/assets/img/metasploitable2/fig4_smtp_scan.png)

This becomes important when attempting SSH brute-force attacks later.

---

## Web Services

### 80/tcp — Apache 2.2.8
Multiple vulnerable web applications were identified, including:

- DVWA  
- phpMyAdmin  
- phpinfo  
- TWiki and Tikiwiki  
- server-status (if accessible)

Directory enumeration using FFUF revealed several accessible endpoints.

![FFUF Directory Scan 80](/assets/img/metasploitable2/fig5_ffuf_80.png)

---

### 8009/tcp — Apache JServ Protocol (AJP13)
Likely vulnerable to the Ghostcat vulnerability (CVE-2020-1938), allowing file read or code execution in certain configurations.

---

### 8180/tcp — Apache Tomcat Manager
FFUF enumeration revealed the following directories:

- `/admin`  
- `/manager`  
- `/webdav`

![FFUF Tomcat](/assets/img/metasploitable2/fig6_ffuf_8180.png)

The Tomcat administration login page was accessible.

![Tomcat Admin Login Page](/assets/img/metasploitable2/fig7_tomcat_admin.png)

Default Tomcat credentials often work, making this a high-value target for code execution via WAR deployment.

---

## Databases

### 3306/tcp — MySQL 5.0.51a
- Historically configured with weak or empty root password  
- Often accessible using default credentials  
- Can expose data or allow code execution depending on privileges  

---

### 5432/tcp — PostgreSQL 8.3.x
- Outdated  
- Commonly misconfigured  
- Potentially exploitable for remote command execution with valid credentials  

---

## File Sharing and Network Services

### 139/tcp and 445/tcp — Samba 3.0.20
This version of Samba is vulnerable to the usermap script command execution flaw (CVE-2007-2447), which allows remote code execution via crafted usernames.

This is one of the most well-known Metasploitable vulnerabilities.

---

### 2049/tcp — NFS
The NFS server exposes exportable directories without proper authentication.

![NFS Exposure](/assets/img/metasploitable2/fig8_nfs.png)

In plain terms the output means:
The entire filesystem is being shared with everyone on the network, with no restrictions.

Attackers can:

- Mount remote shares  
- Access or modify files  
- Potentially plant SSH keys for persistence  

---

## Exploitation-Friendly Services

### 3632/tcp — distccd
This service is vulnerable to remote command execution (CVE-2004-2687). Metasploit provides a reliable exploit for it.

---

### 1099/tcp and 43729/tcp — Java RMI Registry
Exposes remote method invocation endpoints that can be abused to load malicious Java classes.

---

### 6667/tcp — UnrealIRCd
This IRC daemon version is backdoored by default. It accepts a malicious command that spawns a remote root shell.

This is one of the most severe vulnerabilities on the system.

---

### 8787/tcp — Ruby DRb
Exposes Distributed Ruby functionality over the network, which can allow arbitrary method invocation.

---

## GUI Services

### 5900/tcp — VNC
May accept connections without authentication.

### 6000/tcp — X11
X11 exposure can allow screen capture or keystroke logging.

---

# Attack Surface Summary

Metasploitable 2 exposes multiple high-risk services. Some of the most critical include:

| Service | Vulnerability | Severity |
|---------|--------------|----------|
| FTP vsftpd 2.3.4 | Backdoor Remote Code Execution | Critical |
| Samba 3.0.20 | Usermap Script RCE | Critical |
| UnrealIRCd | Built-in Backdoor | Critical |
| distccd | Remote Code Execution | Critical |
| Tomcat Manager | WAR Deployment RCE | High |
| NFS | Auth-less file access | High |
| MySQL | Weak/no authentication | High |
| SMTP | VRFY user enumeration | Medium |
| SSH | Weak credentials | Medium |
| Web applications | Multiple PHP vulnerabilities | High |

---

# Next Steps

With the attack surface fully mapped, the next stage is exploitation.  
The first major exploit I performed was the vsftpd 2.3.4 backdoor, which results in a root shell.

That exploitation process will be documented in the next post.

---

~ Rex
