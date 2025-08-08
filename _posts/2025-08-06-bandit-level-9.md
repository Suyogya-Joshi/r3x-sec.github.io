---
title: "Bandit Level 9 → Level 10"
date: 2025-08-06
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---

**Level 9 → Level 10**

**Concept Explained**  
The goal is to extract the password from `data.txt`. The file contains binary data mixed with human-readable strings. This level is similar to Level 8, but here we need the `strings` command to extract printable characters, and then find the **only line** that appears once.

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

2. List all files in the home directory:

    ```bash
    ls -l
    ```

3. Extract readable text from `data.txt`, sort the lines, and display only the unique one (the password):

    ```bash
    strings data.txt | sort | uniq -u
    ```

    **Flag explanations:**
    - `-u` — only print lines that occur exactly once.  
    - Sorting before `uniq` ensures identical lines are grouped so `uniq` can detect duplicates.

---

**Why `sort` before `uniq`?**  
`uniq` only compares **adjacent lines**. Without sorting, duplicates scattered through the file won’t be removed properly.  
Refer to [Level 8 → Level 9](/bandit-level-8-to-9/) for a detailed explanation.

<details>
<summary>Terminal Output</summary>

Password: FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

</details>

---

**Key Learnings**
- How to extract human-readable strings from binary data with `strings`.
- How to isolate unique lines using `sort` and `uniq`.
- Why pipeline order matters in command chaining.

---

**Why It Matters**
- Filtering logs for repeated or unique entries.  
- Automating data extraction in **CTFs, pentesting labs, and forensics**.  
- Building efficient command-line workflows for security analysis.

---

**Status**  
Password for Level 10 retrieved.  
Onward to [Level 10 → Level 11](/bandit-level-10-to-11/) next.

---

~ Rex
