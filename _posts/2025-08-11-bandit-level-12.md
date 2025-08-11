---
title: "Bandit Level 12 → Level 13"
date: 2025-08-11
categories: [Walkthroughs, OverTheWire]
tags: [bandit, linux, hexdump, Cryptography, bash, ctf, wargames]
layout: post
---

**Level 12 → Level 13**

**Concept Explained**  

The goal of this level is to extract password from a file which is a **hexdump of a file repeatedly compressed in layers**.
It is recommended to make a new directory (in /tmp folder) and copy the file into that directory.

---

**Understanding HexDump**

Hexdump is a simple visual representation of binary data shown in hexadecimal (base 16) form often alongside its ASCII characters.

---

**Command Used**

    ```bash
    ls -l
    cd /tmp

    mkdir <name_of_directory> #Or use {mktemp -d} which will make a directory with a ramdom name
    
    cp /home/bandit12/data.txt . 

    mv data.txt data.hex

    xxd -r data.hex > file1
    
    #If gzip compressed data:
    mv file1 file1.gz
    gunzip file1.gz
    
    #If bzip2 compressed data:
    mv file1 file1.bz2
    bunzip2 file1.bz2
    
    #If POSIX tar archive:
    tar -xf file1
    ```
---

**Solution Steps**

1. Connect to the server:

    ```bash
    ssh bandit12@bandit.labs.overthewire.org -p 2220
    ```

2. List all files in the home directory:

    ```bash
    ls -l
    ```

3. Navigate to the /tmp directory where you will be creating a temporary folder to work with the file listed in the home directory.
 
   ```bash
   cd /tmp
   ```

4. Create a temporary directory, copy the file from the home directory i.e. data.txt and remane it to data.hex (For clarity)

    ```bash
    mkdir <name_of_directory> 

    cp /home/bandit12/data.txt .
     #Here the . represent the new directory that you just created such that it's your current working directory
    
    mv data.txt data.hex  #Renaming  the file
    ```

5. Reverse the hexdump in data.hex file using the `xxd` command with appropriate flag i.e. -r (reverse)

   ```bash
   xxd -r data.hex > file1
    # Reversing the hexdump and saving the output into file1
   ```

6. Checking the type of compression and decompression the file unitl we get the human readabe file i.e ASCII text
    ```bash
    # Check with `file` command to check for the file type then use appropriate command based on the compression.
    file <file_name>
    ```
     Decompression Decision Table:
  If `gzip compressed data`:
```bash
mv file1 file1.gz
gunzip file1.gz
```
 If `bzip2 compressed data`:
```bash
mv file1 file1.bz2
bunzip2 file1.bz2
```
 If `POSIX tar archive`:
```bash
tar -xf file1
```
 If `ASCII text`, `cat file1` to check contents.

<details>
<summary>Terminal Output</summary>

Password: FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn

</details>

---

**Key Learnings**

- Using `hexdump` command with appropriate flag to reverse a hexdump.
- Using appropriate command to decompress a file based on the different compression techniques used.
- Changing file name using `mv` command.

---

**Why It Matters**

Layered extraction and triage used in:
    - CTF reversing.
    - Malware unpacking.
    - Forensics workflows.
- Systematic CLI handling for layered archives and hexdumps.

---

**Status**  

Password for Level 12 retrieved.  
Onward to [Level 13 → Level 14](/bandit-level-13-to-14/) next.

---

~ Rex