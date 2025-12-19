---
title: "Detecting LLMNR & NBT-NS Poisoning — Network Analysis and Log Correlation"
date: 2025-12-18
categories: [Active Directory, Network Analysis, Blue Team]
tags: [llmnr, nbt-ns, detection, wireshark, event-logs, blue-team]
layout: post
---

# Detecting LLMNR & NBT-NS Poisoning — Network Analysis and Log Correlation

In my previous post, I demonstrated how LLMNR and NBT-NS poisoning can be used to capture NTLM hashes in an Active Directory environment. This follow-up focuses on the defensive side: how to detect this attack using network traffic analysis and Windows event logs.

Understanding detection is critical because these attacks often go unnoticed in environments where legacy protocols remain enabled. This post documents the complete detection methodology using packet captures, DNS logs, and authentication telemetry.

---

## The Test Scenario

For this detection exercise, I created a realistic scenario where an SMB share existed on the network, but DNS lacked a host record for the short name.

When a user attempted to access the share using `\\hackme`, the following occurred:

1. DNS query was sent to the Domain Controller
2. DNS returned NXDOMAIN (no record found)
3. Windows fell back to LLMNR/NBT-NS
4. The attacker intercepted the broadcast and responded
5. NTLM authentication was sent to the attacker

This is the exact trigger condition for LLMNR/NBT-NS poisoning.

---

## Confirming the DNS Gap

Before analyzing the attack traffic, I verified the DNS configuration to confirm the hostname was not registered.

### Testing the Missing Hostname

Running nslookup for the target hostname:

```
nslookup hackme
```

**Result:**

```
Server: DC1-REX.REXLABS.local
Address: 192.168.100.100

*** DC1-REX.REXLABS.local can't find hackme: Non-existent domain
```

![nslookup hackme](/assets/img/llmnr-detection/fig_nslookup_hackme.png)

This confirms:
- DNS server 192.168.100.100 (DC1-REX) was queried
- DNS has no record named "hackme"
- DNS returned NXDOMAIN
- This is the exact trigger condition for LLMNR/NBT-NS fallback

---

### Verifying DNS is Working Correctly

To confirm DNS was functioning properly and not experiencing a service issue, I tested resolution of the Domain Controller itself:

```
nslookup DC1-REX
```

**Result:**

```
Server: DC1-REX.REXLABS.local
Address: 192.168.100.100

Name: DC1-REX.REXLABS.local
Address: 192.168.100.100
```

![nslookup DC1-REX](/assets/img/llmnr-detection/fig_nslookup_dc.png)

This confirms:
- DNS is working correctly
- The Domain Controller is properly registered
- The DNS zone is REXLABS.local
- The DC's hostname resolves perfectly

The DNS service was operational. The issue was simply a missing host record, which created the conditions for fallback name resolution.

---

## Detection Method 1: Network Traffic Analysis

The most reliable way to detect LLMNR/NBT-NS poisoning is by capturing and analyzing network traffic. I used two approaches for this.

### Approach 1: Packet Capture on pfSense

I configured packet capture directly on the pfSense firewall with filtering to capture only LLMNR and NBT-NS traffic.

**Filter configuration:**

```
port 5355 or port 137
```

This filter captures:
- Port 5355: LLMNR traffic
- Port 137: NBT-NS traffic

![pfSense Packet Capture](/assets/img/llmnr-detection/fig_pfsense_capture.png)

The captured packets were then downloaded and analyzed in Wireshark. This method is effective because it captures all LLMNR/NBT-NS traffic at the network perimeter without requiring agents on individual hosts.

---

### Approach 2: Direct Wireshark Capture

Alternatively, I captured traffic directly on the network using Wireshark and applied display filters after the capture.

**Wireshark display filters used:**

```
llmnr
udp.port == 137
```

This method provides more granular control and is useful for troubleshooting specific hosts.

---

## Analyzing the Attack Traffic

Once the packet capture was complete, I identified the attack sequence in Wireshark.

### LLMNR Query from Victim

The first indicator was an LLMNR query broadcast from the victim machine.

**Packet details:**

```
Protocol: LLMNR
Info: Standard query A hackme
Source: 192.168.100.103 (victim)
Destination: 224.0.0.252 (multicast)
```

![LLMNR Query](/assets/img/llmnr-detection/fig_llmnr_query.png)

This shows the victim broadcasting a multicast query asking "Who is hackme?" after DNS failed to resolve the hostname.

---

### LLMNR Response from Attacker

Immediately following the query, a malicious response was sent by the attacker.

**Packet details:**

```
Protocol: LLMNR
Info: Standard query response A hackme
Source: 192.168.100.102 (attacker)
Destination: 192.168.100.103 (victim)
```

![LLMNR Response](/assets/img/llmnr-detection/fig_llmnr_response.png)

The attacker's host responded claiming to be "hackme," directing the victim to authenticate to the attacker's IP address.

---

### NBT-NS Broadcast Traffic

In addition to LLMNR, NBT-NS traffic was also observed.

**Packet details:**

```
Protocol: NBNS
Port: UDP 137
Destination: 192.168.100.255 (broadcast)
Info: Name query NB hackme
```

![NBNS Broadcast](/assets/img/llmnr-detection/fig_nbns_broadcast.png)

Multiple responses were received, indicating that both LLMNR and NBT-NS were being exploited simultaneously.

---

## Key Network Indicators

The packet capture revealed the following attack pattern:

1. Victim sends DNS query (gets NXDOMAIN)
2. Victim broadcasts LLMNR multicast query
3. Attacker responds faster than any legitimate host
4. Victim initiates NTLM authentication to attacker IP
5. NTLM handshake completes on attacker system

**Critical observation:**

The workstation failed DNS resolution for the hostname and fell back to LLMNR and NBT-NS. Packet capture shows multicast and broadcast name-resolution traffic, followed by a spoofed response from a non-DNS host. This response caused the victim to authenticate to the attacker, resulting in NTLM credential interception.

---

## Detection Method 2: Windows DNS Client Logs

Windows logs DNS failures that can indicate LLMNR/NBT-NS fallback conditions.

### Event ID 1014

The DNS Client service logs Event ID 1014 when name resolution fails.

**Event details:**

```
Log: Microsoft-Windows-DNS-Client/Operational
Event ID: 1014
Level: Warning
Message: Name resolution for the name hackme timed out after none of the configured DNS servers responded
```

![Event ID 1014](/assets/img/llmnr-detection/fig_event_1014.png)

**Important note:**

In this case, Event ID 1014 was not logged because the DNS response was immediate (NXDOMAIN). The client then fell back to LLMNR/NBT-NS, where a non-DNS host responded, resulting in NTLM credential interception.

Event ID 1014 only appears when DNS queries time out, not when they return NXDOMAIN. This means you cannot rely solely on this event for detection.

---

## Detection Method 3: Authentication Logs

The final layer of detection involves examining authentication telemetry.

### NTLM Credential Capture

NTLM authentication was successfully intercepted by the attacker using Responder. The captured NTLMv2 challenge-response shows the domain user `REXLABS\u.sarah` attempting SMB authentication after the hostname was resolved via LLMNR/NBT-NS.

![Captured Hash Output](/assets/img/llmnr-lab/fig_hash_captured.png)


**Critical finding:**

No corresponding authentication events were recorded on the victim workstation or Domain Controller. The NTLM handshake terminated on the attacker host before reaching any legitimate authentication infrastructure.

This is why LLMNR/NBT-NS poisoning is so dangerous. The attack occurs at the name resolution layer, preventing authentication logs from appearing on the Domain Controller.

---

## Why This Attack Bypasses Standard Logging

Authentication was confirmed via attacker-side evidence and network telemetry. Victim-side authentication logging was not present due to NTLM logging limitations.

The key issue is that:
- The authentication never reaches the Domain Controller
- The victim believes it is authenticating to a legitimate server
- No security event 4625 (failed logon) is generated
- No security event 4624 (successful logon) is generated on the DC

The only place this authentication is visible is:
1. Network packet captures (most reliable)
2. DNS Client operational logs (only for timeouts)
3. Attacker-side capture tools like Responder

---

## The Real Lesson: Name Resolution vs File Sharing

A critical misconception is that this attack only works when shares do not exist. This is incorrect.

**Key takeaway:**

LLMNR/NBT-NS poisoning is a name-resolution problem, not a file-sharing problem.

A share can exist perfectly and still be abused if:
- Users access it via short names instead of FQDNs
- DNS records are missing or incomplete
- Legacy name resolution protocols remain enabled

In this scenario:
- The share did exist on the server
- The environment was mostly configured correctly
- A small DNS gap combined with short hostname usage was enough to enable the attack

This is why organizations must disable LLMNR/NBT-NS entirely, not just ensure proper DNS configuration.

---

## Detection Strategy Summary

To effectively detect LLMNR/NBT-NS poisoning, organizations should implement:

### Layer 1: Network Monitoring
- Deploy packet capture at network chokepoints
- Monitor for LLMNR traffic on port 5355
- Monitor for NBT-NS traffic on port 137
- Alert on responses from unexpected IP addresses

### Layer 2: DNS Logging
- Enable DNS Client operational logging
- Monitor for Event ID 1014 (query timeouts)
- Track NXDOMAIN responses in DNS server logs
- Identify patterns of failed name resolution

### Layer 3: Authentication Monitoring
- Enable NTLM auditing where possible
- Monitor for authentication attempts to non-domain systems
- Correlate authentication events with network traffic
- Investigate authentication gaps (requests without corresponding DC logs)

### Layer 4: Endpoint Detection
- Deploy EDR solutions that monitor name resolution
- Alert on LLMNR/NBT-NS queries from endpoints
- Track processes initiating broadcast name resolution
- Monitor for suspicious outbound SMB connections

---

## Remediation Priority

Based on this detection exercise, the mitigation priorities are:

1. **Disable LLMNR via Group Policy** (highest impact)
2. **Disable NBT-NS via DHCP** (network-level control)
3. **Use FQDNs instead of short names** (user training)
4. **Implement network monitoring** (detection layer)
5. **Regular DNS hygiene** (prevent gaps)

Organizations that rely only on detection without disabling these protocols are accepting unnecessary risk.

---

## Key Takeaways

- LLMNR/NBT-NS poisoning occurs at the name resolution layer
- DNS gaps create exploitable conditions even when shares exist properly
- Network traffic analysis is the most reliable detection method
- Windows event logs have significant gaps for this attack type
- Authentication logs may not appear on the Domain Controller
- The attack succeeds because protocols lack authentication
- Disabling LLMNR/NBT-NS is more effective than trying to detect attacks

---

## Next Steps

This detection analysis complements the initial attack demonstration. Together, these posts provide both red team and blue team perspectives on LLMNR/NBT-NS poisoning.

In the next post, I will explore additional Active Directory attack techniques including Kerberoasting and credential relay attacks.

---

~ Rex