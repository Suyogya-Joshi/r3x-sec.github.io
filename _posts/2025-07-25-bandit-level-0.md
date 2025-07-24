---
title: "Bandit Level 0 → Level 1"
date: 2025-07-25
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, ssh, ctf, wargames]
layout: post
---

 **Level 0 → Level 1**

 **Concept Explained**  
SSH into a remote machine and retrieve a file using basic Linux CLI commands.  
This is the true beginning of any ethical hacker's journey — learning to operate in a terminal-first world.

---

**Commands Used**

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
ls
cat readme
```

---
 **Solution Steps**

1. Connect via SSH:

   ```bash
   ssh bandit0@bandit.labs.overthewire.org -p 2220
   ```

2. List files:

   ```bash
   ls
   ```

3. Read the `readme` file:

   ```bash
   cat readme
   ```

That’s your password for Level 1.

<details>
<summary>Terminal Output</summary>

    bandit0@bandit:~$ ls
    readme
    bandit0@bandit:~$ cat readme
    Congratulations on your first steps into the bandit game!!
    Please make sure you have read the rules at https://overthewire.org/rules/
    If you are following a course, workshop, walkthrough or other educational activity,
    please inform the instructor about the rules as well and encourage them to
    contribute to the OverTheWire community so we can keep these games free!

    The password you are looking for is: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If

</details>


###  Bash Trick Learned

```bash
echo "$( < readme )"
```

 Uses Bash **built-in input redirection**  
 Avoids spawning an extra `cat` process  
 Clean & efficient for scripting

---

###  Speed Comparison

| Method                     | Speed      | Use Case                        |
|----------------------------|------------|----------------------------------|
| `cat readme`               |  Fastest | Best for quick display           |
| `echo "$( < readme )"`     |  Fast     | Great for scripting              |
| `echo "$(cat readme)"`     |  Slow     | Least efficient, avoid in loops  |

---

---

 **Status**  
 Level 0 complete. Password for Level 1 retrieved and noted.  
Let’s move on to [Level 1 → Level 2](/bandit-level-1) next.

---

_~ Rex_
