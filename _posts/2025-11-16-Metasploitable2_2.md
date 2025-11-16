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

nmap -sV -A -p- 192.168.50.63


Where:

- `-p-` scans all 65,535 TCP ports  
- `-sV` enables service version detection  
- `-A` enables OS detection, script scanning, and traceroute  

Nmap returned a large number of open services. Below is the captured scan output:

![Full Nmap Scan](/assets/img/metasploitable2/fig3_nmap_fullscan.png)


<div style="max-height: 500px; overflow-y: scroll;">
<pre><code>
┌──(kali㉿kali)-[~]
└─$ nmap -T 5 -p- -A 192.168.50.63
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-16 03:54 EST
Nmap scan report for 192.168.50.63
Host is up (0.0017s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.50.30
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|_    SSL2_RC2_128_CBC_WITH_MD5
|_ssl-date: 2025-11-16T08:57:13+00:00; -1s from scanner time.
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
53/tcp    open  domain      ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-title: Metasploitable2 - Linux
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      46664/udp   mountd
|   100005  1,2,3      52634/tcp   mountd
|   100021  1,3,4      35711/udp   nlockmgr
|   100021  1,3,4      58041/tcp   nlockmgr
|   100024  1          46513/udp   status
|_  100024  1          57788/tcp   status
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp   open  exec?
513/tcp   open  login       OpenBSD or Solaris rlogind
514/tcp   open  shell?
| fingerprint-strings: 
|   NULL: 
|_    Couldn't get address for your host (g14)
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 8
|   Capabilities flags: 43564
|   Some Capabilities: LongColumnFlag, SwitchToSSLAfterHandshake, SupportsCompression, SupportsTransactions, ConnectWithDatabase, Support41Auth, Speaks41ProtocolNew
|   Status: Autocommit
|_  Salt: I7uh+<,gE_1twwqPy46P
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
|_ssl-date: 2025-11-16T08:57:13+00:00; -1s from scanner time.
5900/tcp  open  vnc         VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
| irc-info: 
|   users: 1
|   servers: 1
|   lusers: 1
|   lservers: 0
|   server: irc.Metasploitable.LAN
|   version: Unreal3.2.8.1. irc.Metasploitable.LAN 
|   uptime: 0 days, 0:13:26
|   source ident: nmap
|   source host: Test-4D2708D2
|_  error: Closing Link: ebscvsmoz[g14] (Quit: ebscvsmoz)
6697/tcp  open  irc         UnrealIRCd
| irc-info: 
|   users: 1
|   servers: 1
|   lusers: 1
|   lservers: 0
|   server: irc.Metasploitable.LAN
|   version: Unreal3.2.8.1. irc.Metasploitable.LAN 
|   uptime: 0 days, 0:13:26
|   source ident: nmap
|   source host: Test-4D2708D2
|_  error: Closing Link: ekfuzwolj[g14] (Quit: ekfuzwolj)
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/5.5
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
52634/tcp open  mountd      1-3 (RPC #100005)
55871/tcp open  java-rmi    GNU Classpath grmiregistry
57788/tcp open  status      1 (RPC #100024)
58041/tcp open  nlockmgr    1-4 (RPC #100021)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port514-TCP:V=7.95%I=7%D=11/16%Time=69199161%P=x86_64-pc-linux-gnu%r(NU
SF:LL,2A,"\x01Couldn't\x20get\x20address\x20for\x20your\x20host\x20\(g14\)
SF:\n");
MAC Address: 08:00:27:81:70:24 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2025-11-16T03:57:04-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 1h14m59s, deviation: 2h30m00s, median: -1s
|_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

TRACEROUTE
HOP RTT     ADDRESS
1   1.66 ms 192.168.50.63

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 146.30 seconds

</code></pre>
</div>


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
