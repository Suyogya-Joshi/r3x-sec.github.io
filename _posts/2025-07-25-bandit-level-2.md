---
title: "Bandit Level 2 → Level 3"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, filenames, bash, ctf, wargames]
layout: post
---

**Level 2 → Level 3**

**Concept Explained**  
This level tests your ability to handle files with **spaces in their names**. A common source of bugs in scripts and misfires in CLI, it’s a great test of command-line precision.

---

**Commands Used**

```bash
ssh bandit2@bandit.labs.overthewire.org -p 2220
ls -la
cat "spaces in this filename"
cat spaces\ in\ this\ filename
```

---

**Solution Steps**

1. SSH into the machine:

   ```bash
   ssh bandit2@bandit.labs.overthewire.org -p 2220
   ```

2. List all files in the home directory:

   ```bash
   ls -la
   ```

3. Read the file using either quoting or escaping:

   ```bash
   cat "spaces in this filename"
   ```

   or

   ```bash
   cat spaces\ in\ this\ filename # Using TAB auto completion is suggested as it is faster than typing entire file name.
   ```

<details>
<summary>Terminal Output</summary>

```bash
bandit2@bandit:~$ ls
spaces in this filename
bandit2@bandit:~$ cat spaces\ in\ this\ filename
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
bandit2@bandit:~$
```

</details>

---

**Bash Trick**

```bash
echo "$( < "spaces in this filename" )"
```

Efficient alternative to `cat`, ideal for scripting.

---

**Key Learnings**

- Quoting or escaping filenames with spaces
- Reinforcing the habit of precision when handling non-standard filenames

---

**Why It Matters (Cybersec)**

- Real-world pentests and malware often use tricky file names
- Forensics and enumeration often encounter these
- Writing scripts that fail on spaces is a common beginner pitfall

---

**Status**  
Password for Level 3 successfully retrieved.  
Onward to [Level 3 → Level 4](/bandit-level-3) next.

---

~ Rex
