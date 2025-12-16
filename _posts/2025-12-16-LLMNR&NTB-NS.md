---
title: "LLMNR & NBT-NS Poisoning — Hands-On Lab, Attack Flow, and Mitigations"
date: 2025-12-16
categories: [Active Directory, Network Attacks]
tags: [llmnr, nbt-ns, responder, ntlm, active-directory, homelab, mitm]
layout: post
---

# LLMNR & NBT-NS Poisoning — Hands-On Lab, Attack Flow, and Mitigations

In this lab, I explored **LLMNR and NBT-NS poisoning**, a classic yet still relevant attack technique in Windows-based enterprise networks.

The goal was to understand why these protocols exist, how attackers exploit them to capture **NTLMv2 hashes**, and why the **Domain Controller is never contacted** during the attack.

This was performed in a controlled **Active Directory homelab** using Proxmox, pfSense, Windows Server 2019, Windows clients, and Kali Linux.

---

## Lab Topology

The environment consisted of:

- **Proxmox VE** (hosting all VMs)
- **pfSense** (routing & DHCP)
- **Windows Server 2019** with AD DS, AD CS, DNS, and File Services
- **Windows 10 Client** (Domain-joined)
- **Kali Linux** (attacker)
- **Raspberry Pi 5** (DHCP/DNS upstream)

![Lab Topology](/assets/img/llmnr-lab/fig_topology.png)

This isolated network allowed safe testing of credential capture techniques without impacting production systems.

---

## Understanding LLMNR and NBT-NS

**LLMNR (Link-Local Multicast Name Resolution)** and **NBT-NS (NetBIOS Name Service)** are legacy fallback name-resolution protocols used when DNS fails to resolve a hostname.

The critical security flaw is that these protocols **do not authenticate responses**.

This allows an attacker on the same subnet to impersonate any hostname and trick Windows machines into sending credential hashes.

Key characteristics:

- Enabled by default on Windows systems
- Broadcast-based communication
- No authentication mechanism
- Trigger automatically after DNS failure
- Commonly exploited in internal network attacks

---

## Attack Flow

The attack follows this sequence:

1. A user attempts to access a network resource (e.g., `\\hackme`)
2. DNS fails to resolve the hostname
3. Windows broadcasts an **LLMNR/NBT-NS request**
4. The attacker responds: "I am that host"
5. The victim sends their username and **NetNTLMv2 hash**
6. The attacker captures the hash
7. The hash can be cracked offline or relayed to another service

The critical point is that **the Domain Controller is never contacted** during this process. The hash is sent directly to the attacker before any authentication occurs.

---

## Setting Up the Attack (Kali Linux)

I used **Responder** to poison name resolution requests and capture hashes.

Starting Responder with the following options:

```bash
sudo responder -I eth0 -wd
```

This command:
- Listens on the eth0 interface
- Enables WPAD rogue proxy (`-w`)
- Enables answers for DHCP requests (`-d`)

Responder listens for LLMNR/NBT-NS broadcasts and responds faster than legitimate hosts, intercepting the authentication attempt.

![Responder Running](/assets/img/llmnr-lab/fig_responder_running.png)

---

## Victim Activity

From a domain-joined Windows 10 machine, I attempted to access a non-existent shared folder:

```
\\hackme
```

This simulates a common user mistake: mistyping a server name or attempting to access a resource that doesn't exist.

Windows could not resolve the hostname via DNS, so it fell back to LLMNR/NBT-NS broadcasting.

![Windows Credential Prompt](/assets/img/llmnr-lab/fig_credential_prompt.png)

The user was prompted to enter credentials, believing they were authenticating to a legitimate server.

---

## Hash Capture

Responder successfully intercepted the broadcast and captured the NetNTLMv2 hash from the client machine.

The captured output showed:

```
[+] Listening for events...
[HTTP] NTLMv2 Hash captured from 192.168.50.100
[HTTP] User: DOMAIN\username
[HTTP] Hash: username::DOMAIN:1122334455667788:hash_value...
```

![Captured Hash Output](/assets/img/llmnr-lab/fig_hash_captured.png)

This hash was automatically saved to Responder's logs directory for offline processing.

---

## Cracking the NTLMv2 Hash

With the hash captured, I used **Hashcat** to attempt offline cracking:

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

Parameters:
- `-m 5600`: Specifies NetNTLMv2 hash type
- `hash.txt`: File containing the captured hash
- `rockyou.txt`: Common password wordlist

For weak or reused passwords, recovery was trivial and completed within minutes.

![Hashcat Cracking Output](/assets/img/llmnr-lab/fig_hashcat_output.png)

Once cracked, the plaintext password could be used for lateral movement, privilege escalation, or further compromise.

---

## Mitigation and Defense

### Disable LLMNR via Group Policy

This is the most effective mitigation in Active Directory environments.

**Path:**
```
Computer Configuration
└─ Administrative Templates
   └─ Network
      └─ DNS Client
         └─ Turn off multicast name resolution
```

Set the policy to **Enabled**.

![GPO Configuration](/assets/img/llmnr-lab/fig_gpo_llmnr.png)

This disables LLMNR on all domain-joined systems and prevents the attack from succeeding.

---

### Disable NBT-NS via DHCP

NBT-NS can be disabled at the network level by configuring DHCP options.

In **pfSense**, configure the following DHCP option:

- **Option**: NetBIOS Node Type
- **Value**: `2` (P-node / Peer-to-Peer)

This prevents NetBIOS name broadcasts across the network.

![pfSense DHCP Options](/assets/img/llmnr-lab/fig_pfsense_dhcp.png)

Setting the node type to P-node forces clients to use WINS servers only, eliminating broadcast-based name resolution.

---

## Why This Attack Still Matters

Even in modern environments, this attack remains relevant because:

- LLMNR and NBT-NS are **enabled by default** on Windows
- Internal attackers only need **network access**
- NTLM hashes are still widely used and abused
- The attack **bypasses the Domain Controller** entirely
- No authentication is required to capture credentials

This makes LLMNR/NBT-NS poisoning a low-effort, high-impact attack vector commonly exploited during internal penetration tests and red team engagements.

---

## Security Impact

If this attack occurred in a production environment, the consequences could include:

- Compromise of user credentials
- Lateral movement across the network
- Privilege escalation opportunities
- Pass-the-hash attacks
- Potential domain-wide compromise

Organizations must prioritize disabling these legacy protocols to reduce their attack surface.

---

## Key Takeaways

- LLMNR and NBT-NS are legacy protocols with no authentication
- Attackers can silently capture NTLM hashes from the local network
- Domain Controllers are not involved in the attack
- Disabling LLMNR via Group Policy is critical
- DHCP-level hardening further reduces risk
- Regular security audits should verify these mitigations are in place

---

## Next Steps

With LLMNR/NBT-NS poisoning understood and mitigated in the lab environment, the next phase will explore additional Active Directory attack techniques including Kerberoasting and AS-REP Roasting.

---

~ Rex