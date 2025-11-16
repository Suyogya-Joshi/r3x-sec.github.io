---
title: "Metasploitable 2 — Reconnaissance & Host Discovery"
date: 2025-11-16
categories: [Pentesting, Metasploitable]
tags: [recon, nmap, arp-scan, enumeration, lab]
layout: post
---

# Metasploitable 2 — Reconnaissance & Host Discovery

In this first stage of the Metasploitable 2 lab, I focused on identifying the target machine on the network and gathering initial information about its presence.  
This post covers **ARP discovery**, **ping sweeps**, and foundational recon techniques used in real penetration tests.

---

## Lab Setup

Before scanning, I set up the following machines:

- **Attacker Machine (Kali Linux)**  
  `192.168.50.30`
  ![Fig : Attacker IP](/assets/img/metasploitable2/AttackerIP.png)


- **Victim Machine (Metasploitable 2)**  
  `192.168.50.63`
  ![Fig : Victim IP](/assets/img/metasploitable2/VictimIP.png)


This matches a standard internal penetration-testing scenario where both systems are on the same LAN.

---

## Discovering Hosts Using ARP-Scan

The first method I used was **ARP-Scan**, a powerful tool for detecting live hosts on a local network segment.

ARP-Scan sends ARP requests to every IP in a range.  
Because ARP operates at Layer 2, the results are *highly reliable* — no firewall can block ARP.

![Fig 1: Using Arp-scan to find the target IP address](/assets/img/metasploitable2/fig1_arp_scan.png)

ARP-Scan quickly returned several live devices, including the Metasploitable target.

### Why ARP-Scan Works So Well

- ARP is required for any device to communicate on a LAN.
- Devices must respond with their MAC address.
- It bypasses many firewall or ICMP restrictions.

In a real pentest, this is often the fastest way to map the local network.

---

## Discovering Hosts Using Nmap Ping Sweep

As a second method, I used:


This is known as a **ping sweep**, which checks which devices respond to ICMP echo requests or ARP pings.

![Using Nmap -sn to find live hosts](/assets/img/metasploitable2/fig2_nmap_sn.png)

### What `-sn` Does
- Disables port scanning.
- Only finds **which hosts are alive**.
- Useful for quickly identifying all active machines before detailed scanning.

Nmap successfully detected the Metasploitable host at `192.168.50.63`, confirming the results from ARP-Scan.

---

## Why Host Discovery Matters

Before touching any service or attempting exploitation, a pentester must:

1. **Identify all active hosts**
2. **Determine what systems are worth targeting**
3. **Avoid scanning irrelevant or unintended devices**
4. **Build an accurate map of the network**

Skipping this step leads to incomplete results and missed attack vectors.

---

## Recon Summary

At the end of the reconnaissance phase, I confirmed:

- The Metasploitable 2 machine is alive on **192.168.50.63**
- Both ARP-Scan and Nmap agree on host discovery
- Network visibility is clear enough to proceed to full port scanning

This sets up the next stage:  
**Deep enumeration and vulnerability mapping using Nmap.**

---

~ Rex
