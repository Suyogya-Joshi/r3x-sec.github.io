---
title: "Bandit Level 7 → Level 8"
date: 2025-07-29
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, pattern-searching, bash, ctf, wargames]
layout: post
---
**Level 7 → Level 8**

**Concept Explained** 
The main goal of this level is to find password which is next to the word `millionth` in a text file called `data.txt`. In this level you will use `grep` command which allows you to search pattern within a file.

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

2. List all the files in the home directory:

  ```bash
  ls -l
  ```

3. Use the  `grep` command to search string pattern(i.e. "millionth") within `data.txt`

  ```bash
  grep "millionth" data.txt
  # grep <pattern> <file_name>
  ```

<details>
<summary>Terminal Output</summary>
    
    Password:  dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc

</details>

---

**Key Learnings**

- Efficient pattern searching with `grep`.
- Handling files with clear command structure.

----

**Why It Matters**

`grep` is essential for:
- Log and output parsing in forensics and incident response.
- Quick keyword hunting during CTFs and pentesting.
- Building CLI parsing workflows in your labs.

---

 **Status**  
Password for Level 8 successfully retrieved.  
Onward to [Level 8 → Level 8](/bandit-level-8) next.

---

~ Rex