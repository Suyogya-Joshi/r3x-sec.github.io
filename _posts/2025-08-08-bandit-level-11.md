---
title: "Bandit Level 11 → Level 12"
date: 2025-08-08
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, ROT13, Cryptography, bash, ctf, wargames]
layout: post
---

**Level 11 → Level 12**

**Concept Explained**  

The goal of this level is to retrive password stored in the `data.txt` file. However, the password has been encypted using a simple encryption technique called `ROT13`.

---

**Understanding ROT13**
- ROT13 is a special case of the Caesar cipher.
- ROT13 is a simple subsitutioin cipher.
- It rotates each of the letter by 13 places.
- Applying ROT13 twice gives us the original text.

Try out the Ceaser Cypher: [Ceaser Cyber](https://www.cryptool.org/en/cto/caesar/)

---

**Command Used**

    ```bash
    ls -la
    cat data.txt | tr "A-Za-z" "N-ZA-Mn-za-m"
    ```

---

**Solution Steps**

1. Connect to the server:

    ```bash
    ssh bandit11@bandit.labs.overthewire.org -p 2220
    ```

2. List the files in the home directory:

    ```bash
    ls -la
    ```

3. Use the command `tr` command which means `translate` to decode the ROT13:
    
    ```bash
    cat data.txt | tr "A-Za-z" "N-ZA-Mn-za-m"
    ```

  Using `tr` to translate:
  - `'A-Za-z'` ➔ all uppercase and lowercase letters.
  - `'N-ZA-Mn-za-m'` ➔ rotated by 13 positions.

<details>
<summary>Terminal Output</summary>

Password: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

</details>

---

**Key Learnings**

-Using `tr` command to decipher the ROT13 text.

---

**Why It Matters**

 Efficient use of `tr` for decoding text systematically.
- Understanding and decoding ROT13, common in:
  - CTF flags.

  ---

**Status**  

Password for Level 12 retrieved.  
Onward to [Level 12 → Level 13](/bandit-level-12-to-13/) next.

---

~ Rex