---
title: "Bandit Level 1 → Level 2"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, cat, stdin, ctf, wargames]
layout: post
---

**Level 1 → Level 2**

**Concept Explained**  
You’re given a file named `-` in your home directory. The challenge is accessing it correctly — without your commands confusing it for a CLI option. This is where your Linux-fu begins to sharpen.

---

**Commands Used**

```bash
ssh bandit1@bandit.labs.overthewire.org -p 2220
ls -la
cat ./-
cat -- ./-
```

---

**Solution Steps**

1. Connect to the Bandit server:

   ```bash
   ssh bandit1@bandit.labs.overthewire.org -p 2220
   ```

2. See what's in the directory:

   ```bash
   ls -la
   ```

3. Read the file named `-` using either:

   ```bash
   cat ./-
   ```

   or

   ```bash
   cat -- ./-
   ```

<details>
<summary>Terminal Output</summary>

  
    Password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
   

</details>

**Bash Trick (Alternative to `cat`)**

```bash
echo "$( < ./- )"
```

Reads the file using Bash built-in input redirection  
No external `cat` process needed  
Useful in scripting contexts or inline formatting

---

**Key Learnings**

- `./-` helps avoid treating `-` as an argument
- `--` tells commands to stop parsing options
- `cat -- -` still reads from stdin, not the file

---

**Why It Matters**

- Files named `-`, `--help`, or other "trick" names are common in CTFs, malware analysis, and forensic work
- Learning how to handle these precisely prevents misreads, bugs, and even exploitation in automation scripts

---

**Status**  
Password for Level 2 retrieved.  
Let’s head over to [Level 2 → Level 3](/bandit-level-2) next.

---

~ Rex
