---
title: "Bandit Level 9 → Level 10"
date: 2025-08-06
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---
**Level 9 → Level 10**

**Concept Explained**
The goal of this level is to extract password in the `data.txt` file. The `data.txt` file contains binary values mixed with human readable string character. This level is similar to how level 8 was done but we need to use one additional  `strings` command which allows us to extract the human readable character from the `data.txt`where the password is the only line of text that occurs only once in the file.

---

**Commands Used**

```bash
ls -l
strings data.txt | sort | uniq -u 
```
---

**Solution Steps**

1. Connect to the server:

   ```bash
   ssh bandit9@bandit.labs.overthewire.org -p 2220
   ```

2. List all the files in the home directory:

  ```bash
  ls -l
  ```

3. Use the `sort` and `uniq` commands with appropriate flags to obtain the password.

  ```bash
   strings data.txt | sort | uniq -u # flag -u prints line of text occuiring only once
  ```

  **Explanation of flags:**

-u : filters only lines that occur exactly once.

**Why `sort` before `uniq`?**

Refer to [Level 8 ](/bandit-level-8) for the answer.

<details>
<summary>Terminal Output</summary>
    
    Password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

</details>

---

**Key Learnings**

- Extracting human readable strings from a file containing binary garbage.
- Sorting file with duplicate lines.
 
 ---

**Why It Matters**

 Filter logs for repeated or unique entries.
- Automate data analysis during **CTFs, pentesting labs, and forensics.**
- Build systematic, efficient pipelines for real-world cybersecurity workflows.

---

 **Status**  
Password for Level 10 successfully retrieved.  
Onward to [Level 10 → Level 11](/bandit-level-10) next.

---

~ Rex