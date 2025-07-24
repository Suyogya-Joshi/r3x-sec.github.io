---
title: "Bandit Level 3 → Level 4"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, hidden-files, bash, ctf, wargames]
layout: post
---

**Level 3 → Level 4**

**Concept Explained**  
You're told the password is hidden in a file within the `inhere` directory. This level focuses on discovering and reading hidden files (files starting with a dot), which is common in both admin and attacker workflows.

---

**Commands Used**

```bash
ssh bandit3@bandit.labs.overthewire.org -p 2220
cd inhere/
ls -la inhere
cat ...Hiding-From-You
```

---

**Solution Steps**

1. Connect to the server:

   ```bash
   ssh bandit3@bandit.labs.overthewire.org -p 2220
   ```

2. Change the Directory and list contents of the `inhere` directory, including hidden files:

   ```bash
   cd inhere/
   ls -la # Here flag/option -a displays all the hidden files which starts with "."
   ```

3. Display the hidden file contents:

   ```bash
  cat ...Hiding-From-You
   ```

<details>
<summary>Terminal Output</summary>

    bandit3@bandit:~$ ls
    inhere
    bandit3@bandit:~$ cd
    bandit3@bandit:~$ cd inhere/
    bandit3@bandit:~/inhere$ ls
    bandit3@bandit:~/inhere$ ls -la
    total 12
    drwxr-xr-x 2 root    root    4096 Apr 10 14:23 .
    drwxr-xr-x 3 root    root    4096 Apr 10 14:23 ..
    -rw-r----- 1 bandit4 bandit3   33 Apr 10 14:23 ...Hiding-From-You
    bandit3@bandit:~/inhere$ cat ...Hiding-From-You
    2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
    bandit3@bandit:~/inhere$

</details>

**Bash Trick**

```bash
echo "$( < inhere/...Hiding-From-You )"
```

This uses Bash's built-in input redirection.  
Efficient and script-friendly for inline file capture without spawning an extra `cat` process.

---

**Key Learnings**

- How to list hidden files using `ls -la`
- How to read dotfiles (files starting with `.`)
- Recognizing that hidden doesn’t mean secure

---

**Why It Matters**

- Hidden files are frequently used to conceal data in malware, pentests, and CTFs
- Sharpens your file enumeration techniques and attention to CLI details

---

**Status**  
Password for Level 4 successfully retrieved.  
Onward to [Level 4 → Level 5](/bandit-level-4) next.

---

~ Rex
