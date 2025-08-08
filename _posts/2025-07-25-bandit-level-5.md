---
title: "Bandit Level 5 → Level 6"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, hidden-files, bash, ctf, wargames]
layout: post
---
**Level 5 → Level 6**

**Concept Explained** 

In this level you need to find the password stored in a file within `inhere` directory. This level introduces a new command `find` which allows you search file or folders with different options. Unlike previous there are multiple directories within `inhere` directory.

---

**Commands Used**

```bash
cd inhere/
ls -la
find ./ -size 1033c
find . -type f -size 1033c
cat maybehere07/.file2
```
---

**Solution Steps**
1. Connect to the server:

   ```bash
   ssh bandit5@bandit.labs.overthewire.org -p 2220
   ```
   
2. Change the Directory and list contents of the `inhere` directory, including hidden files:

   ```bash
   cd inhere/
   ls -la # Here flag/option -l displays detailed decription of files
   ```
3. Use `find` command with some options/flags to find the file which contains the password 

   ```bash
   find ./ -type f -size 1033c # find command searches and displays all files that has size 1033 bytes (-type f & -size 1033c)
   #1033c means 1033 bytes (c = characters)
   ```

4. Display the file contents:
  
   ```bash
   cat maybehere07/.file2
   ```

 <details>
<summary>Terminal Output</summary>
    
    Password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

</details>

---

**Key Learnings**
 **`find`** is powerful for:
- Locating files by size (`-size 1033c`).
- Recursively searching in subdirectories.
- Filtering only files using `-type f`.

----

**Why It Matters**
These skills are directly applicable in:
- **CTFs** (locating hidden flags).
- **Forensic analysis** (finding files matching known indicators).
- **Pentesting enumeration** (locating config or credential files by size).

---

 **Status**  
Password for Level 6 successfully retrieved.  
Onward to [Level 6 → Level 7](/bandit-level-6) next.

---

~ Rex