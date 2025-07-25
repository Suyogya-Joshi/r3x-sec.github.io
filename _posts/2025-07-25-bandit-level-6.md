---
title: "Bandit Level 6 → Level 7"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, hidden-files, bash, ctf, wargames]
layout: post
---
**Level 6 → Level 7**

**Concept Explained** 
In previous levels, you searched files within you own user directories. However, In this level you are supposed to find password which is stored somewhere in the server (file system "/"). you are given the properties of the file and you are supposed to use those properties of file to use find command with proper flags/options to locate file with password in the server.

---

**Commands Used**

```bash
find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
cat /var/lib/dpkg/info/bandit7.password
```
---

**Solution Steps**
1. Connect to the server:

   ```bash
   ssh bandit6@bandit.labs.overthewire.org -p 2220
   ```
   

2. Use the find command with correct flags (i.e. -user, -group etc)

   ```bash
   find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null 
   #Here we have used -user and -group flags to define who owns and what group does the file belong to in the file system
   # (2>/dev/null) Redirects **all error messages (stderr) to `/dev/null`**
   ```
   find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null

**Explanation of flags:**

- -type f → only look for files (not directories)
- -user bandit7 → file must be owned by user bandit7
- -group bandit6 → file must belong to group bandit6
- -size 33c → file must be exactly 33 bytes

2>/dev/null → suppress permission-denied errors
 **What is `/dev/null`?**
 It's a special file that discards everything written to it — like a "black hole" for error messages.

 **What does `2>/dev/null` do?**
It redirects stderr (error output) to /dev/null, hiding noisy permission errors so you only see valid results.


3. Displaying the file content for the password

   ```bash
   cat /var/lib/dpkg/info/bandit7.password
   ```

<details>
<summary>Terminal Output</summary>
    
    Password: morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

</details>

---

**Key Learnings**

- find can combine user, group, and size filters to pinpoint specific files.
- 2>/dev/null is a powerful trick to suppress unwanted output and keep your results clean.
- Understanding file ownership and permissions is crucial for system enumeration.

----

**Why It Matters**
Using `find` with `-user`, `-group`, and `-size` is critical for:
- Enumeration in pentests and privilege escalation.
- Forensics triage and file discovery.
- CTF hidden file challenges.
- Mastering `2>/dev/null` ensures **clean, readable CLI output during real labs.**

---

 **Status**  
Password for Level 7 successfully retrieved.  
Onward to [Level 7 → Level 8](/bandit-level-7) next.

---

~ Rex