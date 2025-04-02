---
title: "wtmpdb Notes"
description: "Usage notes for `wtmpdb`, the modern replacement for utmpdump. Helpful for analyzing legacy login records like wtmp."
tags:
  - blue-team
  - log-analysis
  - forensics
  - wtmp
  - tools
  - linux
  - wtmpdb
---

# wtmpdb

## Overview
`wtmpdb` is a modern utility that parses and queries login history stored in `wtmp`-style binary logs. It replaces the deprecated `utmpdump` from `util-linux`.

- **Useful for:** Tracking login sessions, system boots and shutdowns
- **Commonly used in:** Forensics, incident response, aftermath investigations.

## Why use 'wtmpdb'?

- `utmpdump` has been deprecated and removed from `util-linux`.
- `wtmpdb` is actively maintained and supports **legacy import**.
- Provides a more flexible CLI interface with options for time filtering, JSON output, and formatting.

## Installation

On Kali (2025+), `wtmpdb` should be available via APT:

```bash
sudo apt update
sudo apt install wtmpdb
```

## Importing Legacy wtmp files

Use wtmpdb's import command to convert a legacy binary log into a readable wtmpdb file
```bash
wtmpdb import -f <output filename> <legacy wtmp file>
```

After importing the legacy files to a new database you can query it as usual

```bash
last -f wtmp.db
```

NOTE: ```wtmp``` and ```auth.log``` timestamps may differ due to time zone differences. ```auth.log``` usually uses UTC while ```wtmp``` will use system time.

## Writeups Using `wtmpdb`

```dataview
table file.name as "Writeup", file.link as "Location"
from "Platforms"
where contains(tags, "wtmpdb")
sort file.name asc