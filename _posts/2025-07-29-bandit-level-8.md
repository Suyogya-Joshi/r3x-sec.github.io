---
title: "Bandit Level 8 → Level 9"
date: 2025-07-29
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---
**Level 8 → Level 9**

**Concept Explained** 
In this level you are supposed to find the password in `data.txt` that is the only line occurring exactly once. This level will introduce new commands like `sort` and `uniq` along with the use of `pipe` operator.

---

**Commands Used**

```bash
ls -l
sort data.txt | uniq -u 
```

---

**Solution Steps**
1. Connect to the server:

   ```bash
   ssh bandit8@bandit.labs.overthewire.org -p 2220
   ```

2. List all the files in the home directory:

  ```bash
  ls -l
  ```

3. Use the `sort` and `uniq` commands with appropriate flags to obtain the password.

 ```bash
  sort data.txt | uniq -u #instead of -u flag we can also use -c flag
  ```

**Explanation of flags:**
-u : filters only lines that occur exactly once.
-c : counts the number of duplicate lines

 **Why `sort` before `uniq`?**

- `uniq` only removes adjacent duplicates.  
- If duplicates are not next to each other, `uniq` will not filter them.
- Using `sort` ensures duplicates are adjacent, allowing `uniq -u` to detect and remove duplicates accurately.

<details>
<summary>Terminal Output</summary>
    
    Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

</details>

---

**Key Learnings**
- sorting file with duplicate lines.
 
 ---

**Why It Matters**
 Filter logs for repeated or unique entries.
- Automate data analysis during **CTFs, pentesting labs, and forensics.**
- Build systematic, efficient pipelines for real-world cybersecurity workflows.

---

 **Status**  
Password for Level 9 successfully retrieved.  
Onward to [Level 9 → Level 10](/bandit-level-9) next.

---

~ Rex