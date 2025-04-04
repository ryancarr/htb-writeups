---
tags:
  - htb
  - sherlock
  - blue-team
  - forensics
  - incident-response
  - network-analysis
  - ftp
  - s3
  - brute-force
  - exfiltration
title: Origins (Sherlock 2)
description: Network forensic investigation into a suspected FTP compromise that led to data exfiltration and further attacks.
date: 2025-04-04
---

# ![Origins](Origins.png)Origins (Sherlock 2)

## Scenario Summary

- **Platform:** Hack The Box – Sherlock Series
- **Type:** Network Forensics (PCAP Analysis)
- **Artifacts Provided:** Network capture (`Origins.pcap`)
- **Attack Summary:** FTP brute force → credential compromise → file exfiltration → stolen credentials → S3 data leak → phishing attack

---

## Artifacts Overview

| File          | Purpose                          |
|---------------|----------------------------------|
| `Origins.pcap`| Packet capture for network events|

---

## Timeline of Events

| Timestamp           | Event Summary                                        | Source     |
| ------------------- | --------------------------------------------------- | ---------- |
| YYYY-MM-DD HH:MM:SS | FTP brute force begins from `<attacker_ip>`         | PCAP       |
| YYYY-MM-DD HH:MM:SS | Successful FTP login with compromised credentials   | PCAP       |
| YYYY-MM-DD HH:MM:SS | File transfer using FTP commands                    | PCAP       |
| YYYY-MM-DD HH:MM:SS | Backup SSH server password seen in transfer         | PCAP       |
| YYYY-MM-DD HH:MM:SS | S3 bucket URL accessed or mentioned                 | PCAP/email |
| YYYY-MM-DD HH:MM:SS | Phishing email with internal email address observed | PCAP/email |

---

## Analysis

### 1. Attacker's IP Address
- **Answer:** XXX.XXX.XXX.XXX

### 2. Geolocation (City)
- **Answer:** CityName

### 3. FTP Server Application
- **Answer:** AppName X.X.X

### 4. Brute Force Start Time
- **Answer:** YYYY-MM-DD hh:mm:ss

### 5. Successful FTP Credentials
- **Answer:** username:password

### 6. FTP Command for Exfiltration
- **Answer:** RETR

### 7. Backup SSH Server Password
- **Answer:** PasswordHere

### 8. S3 Bucket URL
- **Answer:** https://bucket-name.region.amazonaws.com

### 9. Phishing Email - Internal Attacker Email
- **Answer:** user@forela.xx.xx

---

## Tools

| Tool       | Purpose                                |
|------------|----------------------------------------|
| Wireshark  | Network packet inspection              |
| TShark     | CLI version of Wireshark               |
| curl       | IP geolocation lookups                 |
| grep       | Filtering exported content             |
| strings    | Extracting readable text from payloads |
| CyberChef  | Decode or analyze encoded payloads     |

---

## Prevention

- Use secure FTP alternatives
- Enforce strong credentials and MFA
- Educate users against phishing
- Monitor and log S3 activity
