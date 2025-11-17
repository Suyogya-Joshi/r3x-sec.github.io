---
title: "Metasploitable 2 — Web Enumeration & Directory Discovery"
date: 2025-11-17
categories: [Pentesting, Metasploitable]
tags: [ffuf, web, enumeration, dirbusting, dvwa, tomcat]
layout: post
---

# Metasploitable 2 — Web Enumeration & Directory Discovery

After mapping out the full attack surface using Nmap, the next phase focused on **web enumeration**.  
Metasploitable 2 exposes several vulnerable web applications across multiple HTTP services, primarily on ports **80** and **8180**. These applications provide multiple entry points for client-side and server-side exploits.

This post documents the directory discovery process and highlights key findings across both web servers.

---

## Enumerating the Web Server on Port 80

The main web server is hosted on **Apache 2.2.8**, running on port 80. Before interacting with the application manually, I performed a directory discovery scan using **FFUF**.

### Directory Busting with FFUF

I used FFUF with a standard wordlist to uncover hidden and accessible directories:

![FFUF Scan on Port 80](/assets/img/metasploitable2/fig5_ffuf_80.png)

This scan revealed several notable directories and pages:

- `/dvwa`
- `/phpinfo.php`
- `/phpMyAdmin`
- `/server-status` (Apache status page)
- `/tikiwiki`
- `/twiki`

These findings indicate multiple vulnerable PHP applications, all of which can be targeted later for command injection, SQL injection, or authentication attacks.

---

## Discovering DVWA (Damn Vulnerable Web Application)

One of the most significant findings on port 80 is **DVWA**, a purposely vulnerable web application commonly used for training.

### DVWA Login Page

The login page clearly exposes default credentials:

![DVWA Login Page](/assets/img/metasploitable2/fig_dvwa_login.png)

Using the provided username and password, I successfully authenticated into the application:

![DVWA Logged In](/assets/img/metasploitable2/fig_dvwa_loggedin.png)

Inside DVWA, several modules will be relevant later:

- Command Injection  
- File Upload  
- SQL Injection  
- XSS (Reflected + Stored)  
- CSRF  
- Weak CAPTCHA  

These vulnerabilities provide multiple routes to remote code execution or shell access.

---

## Additional Findings on Port 80

### phpinfo()

The `/phpinfo.php` page reveals detailed server configuration information such as:

- PHP version  
- Loaded modules  
- Environment variables  
- Server paths  

This type of information disclosure can help refine exploit payloads later.

### phpMyAdmin

The presence of **phpMyAdmin** suggests possible database misconfigurations:

- Weak or default MySQL credentials  
- Privilege escalation via SQL queries  
- Enumeration of MySQL users and schemas  

### Tikiwiki / TWiki

Both platforms are known for historical vulnerabilities including:

- Command execution  
- PHP deserialization flaws  
- File disclosure  
- Authentication bypasses  

These could be leveraged for further exploitation.

---

## Enumerating the Web Server on Port 8180 (Apache Tomcat)

Metasploitable also runs a second HTTP service on port 8180, which serves as the **Apache Tomcat Manager interface**.

### Directory Busting with FFUF on Port 8180

Using FFUF again:

![FFUF Scan on Port 8180](/assets/img/metasploitable2/fig6_ffuf_8180.png)

The following directories were discovered:

- `/admin`  
- `/manager`  
- `/webdav`

### Tomcat Admin Panel

Accessing `/admin` displays the Apache Tomcat administration page:

![Tomcat Admin Login Page](/assets/img/metasploitable2/fig7_tomcat_admin.png)

Tomcat often ships with weak or default credentials such as:


```text
tomcat:tomcat
admin:admin
admin:password
```


If login succeeds, it becomes possible to upload a **malicious WAR file** to obtain remote code execution on the system.

---

## WebDAV on Port 8180

The `/webdav` directory exposes a WebDAV service, which may allow:

- File uploads  
- Unauthorized modifications  
- Arbitrary file creation  

Depending on configuration, this could allow direct deployment of JSP shells or other payloads.

---

# Summary of Findings

Through enumeration on ports **80** and **8180**, the following attack vectors were identified:

- DVWA with multiple built-in vulnerabilities  
- Tikiwiki and TWiki applications  
- phpinfo() and phpMyAdmin information disclosure  
- Apache Tomcat Manager with potential default credentials  
- WebDAV exposed, possibly allowing uploads  
- Multiple PHP applications running outdated versions  

These findings significantly expand the attack surface and provide numerous opportunities for remote code execution or lateral movement.

---

# Next Steps

The next post will cover the first major service exploitation:

**Exploiting the vsftpd 2.3.4 Backdoor for Remote Code Execution**

---

~ Rex
