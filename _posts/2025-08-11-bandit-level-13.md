---
title: "Bandit Level 13 → Level 14"
date: 2025-08-11
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, RSA, SSH, bash, ctf, wargames]
layout: post
---

**Level 13 → Level 14**

**Concept Explained**

In this level you are given the private key for level 14 which you will be using to log in as bandit14 user up unitl now we were using password to log in to each level however this  level require you to use the private RSA key to prove your identity to the server.This  level simulate a *key leak scenario* where the private key has been obtained.The public key is already in `bandit14`'s `authorized_keys`, allowing you to log in using the private key without a password.

---

**Understanding  SSH key Authentication**
SSH key authentication allows secure, password-less login to a server using a key pair (private key on your device, public key on the server).

Authentication Flow
1. Login Request:
    Client sends an SSH login request to the server.

2. Challenge:
    Server sends an encrypted challenge (using your public key).

3. Decrypt & Sign:
    Client uses the private key to decrypt the challenge and create a signature.

4. Verification:
    Server uses the stored public key to verify the signature.

5. Result:
    If verification succeeds → Authentication successful and a secure SSH session is established.

Key Point:

- The private key never leaves your machine.
- The server confirms your identity by checking if you can correctly decrypt/sign using the matching private key.

---

**Command Used**

    ```bash
    ls -l
    file sshkey.private
    ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
    ```

---

**Solution Steps**

1. Connect to the server:

    ```bash
    ssh bandit12@bandit.labs.overthewire.org -p 2220
    ```

2. List all files in the home directory:

    ```bash
    ls -l # Here you will see the SSH private key
    ```

3. Check the file type of the ssh key to verify that it is a RSA Private Key

    ```bash
    file sshkey.private #Output: sshkey.private: PEM RSA private key
    ```

4. (Optional) Checking the file permission of the ssh.private key so that it has appropriate permission. `Chmod 600` (i.e Read permission only)

    ```bash
    ls -l sshkey.private
    ```

5. SSH into the the server as bandit14 using the given private key:

    ```bash
    ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220 
    #-i sshkey.private tells ssh to use the specific ssh private key.
    ```
6. Once logged in as bandit14 navigate to /etc/bandit_pass/ and open the bandit14 file to look at the  password for level 14
    
    ```bash
    cat /etc/bandit_pass/bandit14
    ```

<details>
<summary>Terminal Output</summary>

Password: MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS

</details>

---

**Key Learnings**

SSH Key-Based Authentication:
- Using `-i` to specify private key for login.
- Understanding key-based login vs. password login.
- Real-world context of *using leaked private keys* in pentesting and forensic workflows.

Linux File Understanding:
- Using `cat` to read files.
- Checking and understanding file permissions for security.

---


**Why It Matters**

Builds confidence for:
- Pentesting labs.
- Practical system administration.
- Secure authentication workflows in cybersecurity.
---

**Status**  

Password for Level 13 retrieved.  
Onward to [Level 14 → Level 15](/bandit-level-14-to-15/) next.

---

~ Rex