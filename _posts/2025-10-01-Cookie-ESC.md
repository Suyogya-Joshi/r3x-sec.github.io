---
title: "CySCA 2014 Lab – Part 1: Cookie Editing to Escalate Privileges"
date: 2025-09-05
categories: [Web Security, Labs]
tags: [cookies, burpsuite, privilege-escalation, cysca2014, kali]
layout: post
description: Walkthrough of cookie manipulation in the CySCA2014InABox vulnerable web app, where we escalate privileges by editing client-side cookies.
---

#  CySCA 2014 Lab – Part 1: Cookie Editing

In this lab, we exploit a classic insecure design:  
**storing user roles inside cookies.** By flipping a single value, we escalate privileges and unlock hidden functionality.

This exercise is based on the CySCA2014 Web Penetration challenges, packaged as the
**CySCA2014InABox** vulnerable VM. The target is the *Fortress Certifications* website, a
deliberately insecure web app where normal users cannot access the **Blog** section. Our
objective is to exploit the site’s insecure cookie design, escalate to admin, and capture
the hidden flag.

---

##  Lab Setup

- **Attacker:** Kali Linux (with BurpSuite + IceWeasel) 

 ![Kali](assets/img/Cookie/part1/Kali_initial.png)

- **Target:** CySCA2014InABox VM (Fortress Certifications website)  

 ![CYSCA](assets/img/Cookie/part1/CYSCA_initial.png)

Initial observation: all pages are accessible **except the Blog page** (restricted to admin).

![Website](assets/img/Cookie/part1/index_page.png)

---

##  Inspecting Cookies

### Why Cookies Matter

Websites use cookies to store session data and user preferences. A secure design
stores only a random session ID in the cookie and keeps user roles on the server.
However, this lab demonstrates a flawed design where the cookie directly stores the
user's privilege level (`vip=0` for normal, `vip=1` for admin).

1. Configure **IceWeasel** to use Burp as a proxy:  
Edit → Preferences → Advanced → Network → Settings → Manual Proxy Configuration
Host: 127.0.0.1 Port: 8080

![Burp_Suite](assets/img/Cookie/part1/Burp_Website_config.png)


2. Browse to `http://192.168.100.210/index.php` and click **Login**.  
This generates traffic visible in **Burp Proxy → HTTP History**.

![Burp_Suite](assets/img/lab5/part1/Burp_history.png)

3. Inspect the **Raw request** to `login.php`.  
We find two cookie parameters:

- `PHPSESSID=xyz123` → session ID  
- `vip=0` → privilege level 

![Burp_Suite](assets/img/lab5/part1/Burp_raw_request.png)

---

## Exploiting the Cookie

In Burp → Project Options → Session → Session Handling Rules → Cookie Jar, enable → Edit, Scope → Select `Proxy` in Tools scope :

![Burp_Suite](assets/img/lab5/part1/Burp_Session_Handling.png)

“Use cookies from Burp’s cookie jar”.

Scroll to the Cookie Jar, locate vip, and edit:

vip=0  →  vip=1

![Burp_Suite](assets/img/lab5/part1/Burp_Cookie_jar.png)

Refresh the site in IceWeasel.

---

## Result
A new Blog tab appears.

![Website](assets/img/lab5/part1/Website_Blog.png)

Opening it reveals the flag, proving the privilege escalation worked.

---

## Why It Worked
Insecure design (lab target):

```http
Cookie: PHPSESSID=abc123; vip=0
```

The server trusts the client’s vip value directly.
Editing it to 1 gives admin privileges.

Secure design (best practice):

- Cookie stores only a session ID.
- Server maps session → user → role in a database.
- Client-side edits cannot change privileges.

---

## Reflection

After resetting cookies, the Blog page disappeared again.
This confirms that the backend checks the cookie value on each request instead of storing roles securely server-side.

---

## Key Takeaway:

Never trust client-side cookies for privilege management.
Always enforce roles and permissions server-side.

---

In [Part 2: Stored XSS and Session Hijacking](link-to-part2), I’ll show how we escalate further by stealing the admin’s session cookie.

---

~ Rex