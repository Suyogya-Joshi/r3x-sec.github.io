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

---
 **Code Block and Notes**

### 🪄 Bash Trick Learned

```bash
echo "$( < readme )"
```

✅ Uses Bash **built-in input redirection**  
✅ Avoids spawning an extra `cat` process  
✅ Clean & efficient for scripting

---

### 🚀 Speed Comparison

| Method                     | Speed      | Use Case                        |
|----------------------------|------------|----------------------------------|
| `cat readme`               |  Fastest | Best for quick display           |
| `echo "$( < readme )"`     |  Fast     | Great for scripting              |
| `echo "$(cat readme)"`     |  Slow     | Least efficient, avoid in loops  |

---

---

📘 **Status**  
✅ Level 0 complete. Password for Level 1 retrieved and noted.  
Let’s move on to [Level 1 → Level 2](/bandit-level-1) next.

---

_~ Rex_
