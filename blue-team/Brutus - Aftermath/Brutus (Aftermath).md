---
tags:
  - htb
  - blue-team
  - forensics
  - incident-response
  - log-analysis
  - ssh
  - persistence
  - aftermath
  - wtmpdb
title: Brutus (Aftermath - Sherlock 1)
description: Post-incident forensic analysis of the first Sherlock - Brutus. A zip containing an auth.log and wtmp must be analyzed to answer the questions.
date: 2025-04-01
---

# ![Brutus](Brutus.png)Brutus (Aftermath - Sherlock 1)

## Scenario Summary

- **Platform:** Hack The Box – Sherlock Series
- **Type:** Aftermath analysis
- **Artifacts Provided:** Log files (`auth.log`, `wtmp`)
- **Attack Summary:** SSH brute force → privilege escalation → persistence via new user

---

## Artifacts Overview

| File         | Purpose                        |
|--------------|--------------------------------|
| `auth.log`   | Tracks authentication attempts |
| `wtmp`       | Login history (who logged in)  |

---

## Timeline of Events

| Timestamp           | Event Summary                              | Source   |
| ------------------- | ------------------------------------------ | -------- |
| 2024-03-06 06:31:31 | Brute force begins from `<attacker_ip>`    | auth.log | 
| 2024-03-06 06:32:45 | Successful login as `<user>`               | auth.log |
| 2024-03-06 06:32:45 | Manual login tracked in wtmp               | wtmp     |
| 2024-03-06 06:32:45 | Session #<XX> assigned to attacker session | auth.log |
| 2024-03-06 06:34:18 | New user `<new_user>` created via sudo     | auth.log |
| 2024-03-06 06:37:24 | Session ends (auth.log disconnect)         | auth.log |

---

## Analysis

### 1. Brute Force Origin
- **Source IP:** [Redacted]
- **Method of detection:** Searched for repeated failed login attempts in `auth.log`.
- **Command used:** `cat auth.log | grep "disconnect from"`
- **What to look for:** Repeated disconnects from the same IP in a short time and each attempt ends in [preauth] meaning authentication wasn't successful. One or two might be a typo, more than that becomes worth investigation.

### 2. Compromised Account
- **User:** [Redacted]
- **What to look for:** First successful login after brute force pattern. First disconnect log entry that didn't end in [Preauth] means they were able to log in.

### 3. Manual Session (wtmp)
- **Login Time:**[Redacted]
- **Commands Used:** [[wtmpdb]]

  ```bash
  wtmpdb import -f brutus.db wtmp
  last -f brutus.db --time-format full 
  ```
  
- **What to look for:** After the brute force succeeds the first login is probably automated. Look for login and disconnects occurring in the same second. Manual logins will have a few seconds or minutes between login and disconnect.

> [!note]
> Timestamps will differ between auth.log and wtmp on this box. We want the server time, not UTC. `auth.log` often uses UTC time for its timestamps.

### 4. SSH Session ID
- **Session Number:** [Redacted]
- **Command Used:** `cat auth.log | grep "New session"`
- **What to look for:** Compare timestamps to the one we already flagged as suspicious. After a username/password combination is accepted you'll see three log entries back to back.
  1. `Accepted password for <user> from <ip> ...`
  2. `pam_unix(sshd:session): session opened ...`
  3. `sshd[PID]: New session <XX> of user <user>`

> [!tip] Session IDs can be matched to other activities the attacker may use like `sudo` or `useradd`.

---

## Persistence Tactics

### 5. Backdoor User Created
- **New Username:** [Redacted]
- **Command Used:** `cat auth.log | grep useradd`
- **What to look for:** `new user: name=<username> ...` A new user has been created and assigned to a group that was created just before this. Further log entries show a new password was assigned, user information was updated and `usermod` was run to grant `sudo` permissions.

### 6. MITRE ATT&CK Technique
- **ID:** [Redacted]
- **Name:** [Create Account: Local Account](https://attack.mitre.org/techniques/T1136/001/)
- **Why?:** Creating a normal looking account allows an attacker to blend in with other normal users that may be using the machine. It could help avoid detection for longer.

---

## Sudo Action (Downloaded Script)

- **Command Used:**  `cat auth.log | grep "sudo"`
- **What to look for:** `sudo: <username>: ... COMMAND=` This tells us what command the user ran with elevated permissions. In this case they used curl to download `linper` from github.
> [!note]
> `linper.sh` is a persistence framework. A Bash script that helps attackers maintain access by setting up multiple persistence methods.

---

## Tools

| Tool       | Purpose                                                         |
| ---------- | --------------------------------------------------------------- |
| grep       | Searching log entries                                            |
| last       | Reading wtmpdb login history                                    |
| [[wtmpdb]] | Modern replacement for utmpdump, used to read legacy wtmp files |
| less       | Reading large files                                             |
| cat        | Display the contents of a file                                  |

---

## Prevention

- **Disable ssh root login:** This attack leveraged root access to gain a foothold and establish persistence. Fortunately, it's easy to prevent this with a simple SSH configuration change.
  1. Ensure at least one user account has sudo access. We don't want to disable root login and lock ourselves out.
  2. `sudo nano /etc/ssh/sshd_config`, find and modify the line so it reads `PermitRootLogin no`, save the file
  3. Restart SSH `sudo systemctl restart ssh`, this forces ssh to load the new config file.