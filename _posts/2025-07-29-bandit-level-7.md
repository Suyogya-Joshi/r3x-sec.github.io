---
title: "Bandit Level 7 → Level 8"
date: 2025-07-29
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---

**Level 7 → Level 8**

**Concept Explained**  

The goal is to find the password located next to the word `millionth` in `data.txt`. This level uses the `grep` command to search for patterns inside files.

---

**Commands Used**

```bash
grep "millionth" data.txt
```

---

**Solution Steps**

1. Connect to the server:

    ```bash
    ssh bandit7@bandit.labs.overthewire.org -p 2220
    ```

2. List all files in the home directory:

    ```bash
    ls -l
    ```

3. Search for the string `"millionth"` inside `data.txt`:

    ```bash
    grep "millionth" data.txt
    # Syntax: grep <pattern> <file_name>
    ```

<details>
<summary>Terminal Output</summary>

Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

</details>

---

**Key Learnings**

- Efficient pattern searching with `grep`.
- Quickly extracting targeted information from large files.
- Understanding the basic syntax of `grep`.

---

**Why It Matters**

`grep` is essential for:
- Log and output parsing in forensics and incident response.
- Quick keyword hunting during CTFs and pentesting.
- Building CLI parsing workflows in labs and automation scripts.

---

**Status**  

Password for Level 8 retrieved.  
Onward to [Level 8 → Level 9](/bandit-level-8-to-9/) next.

---

~ Rex