---
title: "Bandit Level 10 → Level 11"
date: 2025-08-08
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, base64, bash, ctf, wargames]
layout: post
---

**Level 10 → Level 11**

**Concept Explained**  

The objective is to decode Base64-encoded text in `data.txt` to get the password for the next level. You’ll use the `base64` command with the `-d` (decode) flag.

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

2. List the files in the home directory:

    ```bash
    ls -l
    ```

3. Decode the Base64 content in `data.txt`:

    ```bash
    base64 -d data.txt
    ```

    **Flag explanation:** 

    `-d` — decode input (string or file) into human-readable text.

---

**Understanding the base64 encoding**

Base64 encoding is a process of representing binary values in a text format using only ASCII characters.
(Note: base64 is encoding, not encryption)

**Why Base64 matters**

- Used in text-based protocols (HTTP, SMTP, JSON).
- Embedding assets in HTML/CSS (e.g., data URIs).
- HTTP Basic Auth credentials (header value is Base64).
- Storing binary data in JSON/XML.

**Why Base64 if everything's binary eventually?**

Yes, Eventually all the data is going to be binary data while transferring in physical layer. However, Base64 solves the issues at upper layer where protocols like that in application layer (e.g. HTTP, SMTP) can't handle raw binary data hence there is need to encode the binary values into ascii text before being processed by lower layer.

<details>
<summary>Terminal Output</summary>

Password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

</details>

---

**Key Learnings**

- How to decode Base64 with `base64 -d` and recognize when to use it.

---

**Why It Matters**

- Common in CTFs, malware analysis, exfil decoding, and forensics.

---

**Status**  

Password for Level 11 retrieved.  
Onward to [Level 11 → Level 12](/bandit-level-11-to-12/) next.

---

~ Rex