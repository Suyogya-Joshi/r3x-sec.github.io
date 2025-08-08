---
title: "Bandit Level 8 → Level 9"
date: 2025-07-29
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---

**Level 8 → Level 9**

**Concept Explained**  

In this level, you must find the password in `data.txt` that appears **only once**. This challenge introduces the `sort` and `uniq` commands, along with the `|` (pipe) operator to chain commands.

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

2. List all files in the home directory:

    ```bash
    ls -l
    ```

3. Sort the file and filter lines that occur exactly once:

    ```bash
    sort data.txt | uniq -u
    ```

    **Flag explanations:**
    - `-u` — only print lines that occur exactly once.  
    - `-c` — count occurrences of each line (alternative to `-u` for verification).

---

**Why `sort` before `uniq`?**

- `uniq` only compares **adjacent** lines.  
- If duplicates are scattered in the file, `uniq` will miss them.  
- Sorting first ensures duplicates are grouped together so `uniq` can filter them correctly.

<details>
<summary>Terminal Output</summary>

Password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

</details>

---

**Key Learnings**

- How to use `sort` and `uniq` together to isolate unique values.
- Why command order matters in pipelines.

---

**Why It Matters**

- Filtering logs for repeated or unique entries.  
- Automating data analysis in **CTFs, pentesting labs, and forensics**.  
- Building efficient command-line workflows for real-world cybersecurity tasks.

---

**Status**  

Password for Level 9 retrieved.  
Onward to [Level 9 → Level 10](/bandit-level-9-to-10/) next.

---

~ Rex