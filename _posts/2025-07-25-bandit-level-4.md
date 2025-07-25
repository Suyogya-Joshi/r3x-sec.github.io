---
title: "Bandit Level 4 → Level 5"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, hidden-files, bash, ctf, wargames]
layout: post
---
**Level 4 → Level 5**

**Concept Explained** 
The goal of this level is to retrieve the password stored in the `inhere` directory. There are multiple files from which you have to find the file with the password other file contains garbage value.

---

**Commands Used**

```bash
cd inhere/ 
ls -l  
file ./* 
file ./* | grep "ASCII text" 
```
---

**Solution Steps**

1. Connect to the server:

   ```bash
   ssh bandit4@bandit.labs.overthewire.org -p 2220
   ```
   
2. Change the Directory and list contents of the `inhere` directory, including hidden files:

   ```bash
   cd inhere/
   ls -la # Here flag/option -l displays detailed decription of files
   ```

3. Checking for the file type for each file in the `inhere` directory

   ```bash
   file ./* # Displays detailed info about the type of file in the  current directory ( "*" referes to all the file)
   ```

    or 

   ```bash
   file ./* | grep "ASCII text" #  Optional filtering uisng grep which only shows file ASCII files, which is a human readable 
   ```

4. Display the file contents (File type: ASCII TEXT):
  
  ```bash
   cat ./-file07
   ```

   details>
<summary>Terminal Output</summary>
    
    Password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

</details>

---

**Key Learnings**

- Using `file` to **identify file types before reading unknown files**.
- Using `grep` to filter `file` outputs.
- Understanding the value of `cd` to simplify paths.
- Builds safe enumeration habits when working with unknown file structures.

---

**Why It Matters**
`file` is essential for:
- CTFs
- Forensics
- Pentesting enumeration
 -Reinforces *systematic, safe, and scriptable CLI workflows* for your lab practice.
 
 ---

 **Status**  
Password for Level 4 successfully retrieved.  
Onward to [Level 5 → Level 6](/bandit-level-5) next.

---

~ Rex