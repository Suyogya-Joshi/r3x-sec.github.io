---
title: "Bandit Level 10 → Level 11"
date: 2025-08-06
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, binary-encoding, bash, ctf, wargames]
layout: post
---
**Level 9 → Level 10**

**Concept Explained**
The objective of this level is to decode base64 encoded text stored in `data.txt` to obtain password for the next level. This level require you to use the `base64` command with appropriate flag to decode the encoded text.

---

**Commands Used**

```bash
ls -l
base64 -d data.txt
```
---
**Solution Steps**

1. Connect to the server:

   ```bash
   ssh bandit10@bandit.labs.overthewire.org -p 2220
   ```

2. List all the files in the home directory:

  ```bash
  ls -l
  ```

3. Use the `base64` command with flag `-d` to decode the encoded text which contains the password for next level:

  ```bash
  base64 -d data.txt
  ```

  **Explanation of flags:**

-d : decodes the input i.e string or file into human readable text.

**Understanding the base64 encoding**
Base64 encoding is a process of representing binary values in a text format using only ASCII characters.
(Note: base64 is encoding, not encryption)

**Why base64 encoding matters**
- It is used in data transmission over text-based protocols (i.e. HTTP, SMTP, or JSON).
  Example: If we need to send an image over email (i.e. SMTP), it needs to be encoded as Base64 first.
- Embedding files in text documents (Example: Embedding images directly into HTML or CSS using base64.)
- Encoding credentials in HTTP Basic authentication.
- Storing binary data in JSON or XML.

**Why Base64 if everything's binary eventually?**
Yes, Eventually all the data is going to be binary data while transferring in physical layer. However, Base64 solves the issues at upper layer where protocols like that in application layer (e.g. HTTP, SMTP) can't handle raw binary data hence there is need to encode the binary values into ascii text before being processed by lower layer.

<details>
<summary>Terminal Output</summary>
    
    Password:  dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

</details>

---

**Key Learnings**

- How to decode base64 encoded data into human readable text using base64 command.
 
 ---

 **Why It Matters**
 Base64 decoding is essential in:
- CTF challenges.
- Malware analysis.
- Extracting and decoding exfiltrated data.
- Forensics workflows.

---

 **Status**  
Password for Level 10 successfully retrieved.  
Onward to [Level 11 → Level 12](/bandit-level-11) next.

---

~ Rex