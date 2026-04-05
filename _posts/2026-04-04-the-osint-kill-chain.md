---
title: "The OSINT Kill Chain — From a Username to a Complete Dossier"
description: "The complete OSINT operator's framework: 7 phases from target identification to intelligence production. Username correlation, infrastructure mapping, email forensics, geolocation, credential leak discovery, cryptocurrency tracing, and OPSEC — with tools, commands, and real-world tradecraft at every step."
author: 0xnegan
date: 2026-04-04 14:00:00 +0200
categories: [OSINT, Intelligence]
tags: [osint, reconnaissance, intelligence, sherlock, maltego, shodan, google-dorks, email-forensics, geolocation, exiftool, cryptocurrency, opsec, sock-puppet, mitre-attack, threat-intelligence]
pin: true
image:
  path: /assets/img/blog/osint-kill-chain-banner.png
  alt: "The OSINT Kill Chain — 7-Phase Intelligence Framework"
---

> *Knock knock.*

You don't need to break in when the door was never closed. The target's username is on GitHub. Their email is in a conference PDF. Their home address is embedded in a photo they posted last Tuesday. Their infrastructure is indexed on Shodan. Their leaked credentials are sitting on Pastebin from 2019.

Open-Source Intelligence isn't hacking. It's *reading* — reading what the target already gave away, connecting dots they didn't know existed, and building a picture they never intended anyone to see.

This is the OSINT Kill Chain — a structured 7-phase framework that takes you from a single data point (a name, a username, a domain, an IP) to a complete intelligence dossier. Every phase has tools. Every tool has commands. Every command has output you can pivot from.

Whether you're a red teamer building target profiles for initial access, a threat intelligence analyst tracking adversary infrastructure, or a blue teamer understanding what attackers see when they look at your organization — this is the playbook.

---

## The Framework: 7 Phases of the OSINT Kill Chain

```
┌─────────────────────────────────────────────────────────────────┐
│                    THE OSINT KILL CHAIN                         │
│                                                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────┐  │
│  │ PHASE 1  │  │ PHASE 2  │  │ PHASE 3  │  │   PHASE 4     │  │
│  │ Target   ├─►│ Digital  ├─►│ Infra    ├─►│   Document    │  │
│  │ ID &     │  │ Footprint│  │ Recon    │  │   & Metadata  │  │
│  │ Username │  │ Mapping  │  │          │  │   Forensics   │  │
│  │ Correl.  │  │          │  │          │  │               │  │
│  └──────────┘  └──────────┘  └──────────┘  └───────┬───────┘  │
│                                                     │          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │          │
│  │ PHASE 7  │  │ PHASE 6  │  │ PHASE 5  │◄─────────┘          │
│  │ Intel    │◄─┤ Crypto & │◄─┤ Credential                     │
│  │ Product- │  │ Financial│  │ Leak &                          │
│  │ ion &    │  │ Tracing  │  │ Breach                          │
│  │ Reporting│  │          │  │ Discovery                       │
│  └──────────┘  └──────────┘  └──────────┘                      │
│                                                                 │
│  ════════════════════════════════════════════════════════════   │
│  OPSEC LAYER — Active throughout all phases                     │
│  Sock puppets · VPN/Tor · Browser isolation · Non-attribution   │
└─────────────────────────────────────────────────────────────────┘
```

| Phase | Focus | Key Output |
|---|---|---|
| **1. Target Identification** | Username correlation, identity resolution | Cross-platform account map |
| **2. Digital Footprint Mapping** | Social media, forums, public posts | Behavioral profile, connections |
| **3. Infrastructure Reconnaissance** | DNS, WHOIS, Shodan, CT logs | Network topology, tech stack |
| **4. Document & Metadata Forensics** | EXIF, PDF metadata, geolocation | Physical locations, device info |
| **5. Credential Leak Discovery** | Breach databases, paste sites | Compromised credentials, patterns |
| **6. Cryptocurrency & Financial Tracing** | Blockchain analysis, wallet tracking | Transaction graph, financial links |
| **7. Intelligence Production** | Analysis, reporting, visualization | Finished intelligence product |

The OPSEC layer runs underneath everything. Before you collect a single data point, your investigative posture must be airtight.

---

## Phase 0: OPSEC — Before You Start

Before touching any target, establish your investigative posture. Every search, every page visit, every API call leaves a trace. If your target is monitoring their own digital footprint (and sophisticated targets do), sloppy OPSEC burns the investigation before it starts.

**The Three Pillars:**

**Identity Isolation** — Never use personal accounts for investigation. Build *sock puppets*: carefully constructed research personas with consistent backstories, AI-generated profile photos (ThisPersonDoesNotExist), and unique browser profiles. Each investigation gets its own persona.

**Infrastructure Isolation** — Dedicated VPN or Tor for all investigative traffic. A *dirty line* (separate internet connection not associated with your organization). Virtual machines that can be destroyed after the investigation.

**Browser Fingerprint Control** — Every browser leaks metadata: screen resolution, installed fonts, timezone, WebGL renderer. Use the Tor Browser or Mullvad Browser for standardized fingerprints. Clear cookies between sessions. Never log into personal accounts from investigation browsers.

**The Non-Attribution Test:** Before every action, ask: *"If the target sees this in their logs, can they trace it back to me or my organization?"* If the answer is anything other than "no," fix your posture.

---

## Phase 1: Target Identification & Username Correlation

You start with a seed — a name, a username, an email, a phone number. Phase 1 turns that single seed into a map of every platform the target has ever touched.

### Sherlock — Cross-Platform Username Search

Sherlock queries 400+ social networks and websites simultaneously for a given username, returning confirmed profile URLs.

```bash
# Install
git clone https://github.com/sherlock-project/sherlock.git
cd sherlock && pip install -r requirements.txt

# Run against target username
python3 sherlock <target_username>

# Output: list of confirmed profiles with URLs
# [+] GitHub: https://github.com/<target>
# [+] Pastebin: https://pastebin.com/u/<target>
# [+] HackerOne: https://hackerone.com/<target>
# [+] Cracked Forum: https://cracked.sh/<target>
```

**Maigret** is the modern alternative — same concept, more platforms, profile data extraction:

```bash
pip install maigret
maigret <target_username> --all-sites
```

### What You're Looking For

The raw list of profiles is the starting point. The real intelligence comes from *cross-referencing* them:

- **Hacking forums** (Cracked, RaidForums archives) — reveals technical capability and interests
- **Paste sites** (Pastebin, Ghostbin, ControlC) — may contain code snippets, leaked data, or notes
- **Developer platforms** (GitHub, GitLab, BitBucket) — commit history, email addresses in commits, project interests
- **Social media** — behavioral patterns, connections, location indicators, activity timestamps

### Google Dorking for Identity Expansion

When automated tools miss a platform (Facebook blocks most scrapers), Google Dorks fill the gap:

```
# Find profiles on specific platforms
site:facebook.com "<target_username>"
site:linkedin.com/in "<target_username>"

# Find email addresses associated with a username
"<target_username>" "@gmail.com" OR "@protonmail.com"

# Find forum posts
intext:"<target_username>" site:forum.* OR site:community.*

# Find cached/deleted profiles
cache:twitter.com/<target_username>
```

**The `site:` operator** restricts results to a specific domain. **`inurl:`** searches within URLs. **`filetype:`** finds specific file types. **`intitle:`** searches page titles. These are the four core OSINT dorking operators.

### Pivot Points

Every confirmed profile is a pivot point. Extract:

- **Display name** — may differ from username, revealing real identity
- **Bio/About** — location, employer, interests, links to other platforms
- **Profile photo** — reverse image search with TinEye or Google Images
- **Activity timestamps** — reveals timezone and active hours
- **Connections/followers** — maps social graph

---

## Phase 2: Digital Footprint Mapping

With the account map built, Phase 2 goes deeper into each confirmed platform to extract behavioral intelligence.

### Social Media Intelligence (SOCMINT)

Platform-specific analysis reveals patterns:

- **LinkedIn** — employer, job history, skills, education, connections, endorsements
- **GitHub** — repositories, commit history, collaborators, email in commits (`git log --format='%ae'`), coding languages
- **Twitter/X** — opinions, political leanings, personal relationships, location check-ins, photo metadata
- **Reddit** — subreddit participation reveals interests, comment history reveals opinions and technical knowledge
- **Discord** — server memberships, message history (if accessible), roles

### SpiderFoot — Automated Footprint Aggregation

SpiderFoot connects to 200+ data sources simultaneously. Give it a seed (username, domain, email, IP) and it builds the entire footprint automatically.

```bash
# Install
pip install spiderfoot

# Run with web UI
sf -l 127.0.0.1:5001

# Or CLI scan
sf -s <target_domain> -t all -o json
```

SpiderFoot correlates data across sources — if it finds an IP, it automatically checks blacklists, geolocation, and associated domains. If it finds an email, it checks breach databases and social platforms.

### Maltego — Visual Link Analysis

Where SpiderFoot aggregates, Maltego *visualizes*. It creates interactive graphs showing relationships between entities — people, domains, IPs, email addresses, social media accounts.

Maltego's power is in **Transforms**: automated scripts that take one entity and discover related entities. Start with a domain → transform to DNS records → transform to IPs → transform to other domains on the same IP → transform to WHOIS registrants.

The visual graph makes connections obvious that would be invisible in a spreadsheet.

---

## Phase 3: Infrastructure Reconnaissance

Infrastructure recon maps the target's technical footprint — domains, subdomains, IP addresses, hosting providers, exposed services, and historical records.

### DNS Reconnaissance

DNS records are the backbone of infrastructure mapping:

```bash
# A record — domain to IPv4
nslookup <target_domain>
dig A <target_domain>

# NS record — authoritative name servers
dig NS <target_domain>
# Why it matters: shared name servers link related domains

# MX record — mail servers
dig MX <target_domain>
# Reveals email infrastructure (Google Workspace, Microsoft 365, self-hosted)

# TXT record — SPF, DKIM, domain verification
dig TXT <target_domain>
# SPF records list authorized sending IPs — reveals email infrastructure

# PTR record — reverse DNS (IP to hostname)
dig -x <ip_address>
# The "true" hostname assigned by the hosting provider

# All records at once
dig ANY <target_domain>
```

**Key insight:** When two seemingly unrelated domains share the same private name servers, they're likely owned by the same entity — even with WHOIS privacy enabled.

### Certificate Transparency Logs

Every SSL/TLS certificate issued is logged in public Certificate Transparency logs. These logs reveal subdomains that aren't linked anywhere on the target's website.

```bash
# Search CT logs via crt.sh
curl -s "https://crt.sh/?q=%25.<target_domain>&output=json" | jq '.[].name_value' | sort -u

# Reveals hidden subdomains:
# dev.target.com
# staging.target.com
# vpn.target.com
# internal-api.target.com
```

Subdomains like `dev`, `staging`, `vpn`, and `admin` are goldmines — they often run older software, have weaker authentication, or expose internal services.

### Shodan — The Search Engine for Devices

Shodan indexes every internet-connected device by scanning ports and capturing service banners. It reveals what's exposed without you ever touching the target's network — pure passive reconnaissance.

```bash
# Search by IP
shodan host <ip_address>

# Search by organization
shodan search "org:<company_name>"

# Find specific services
shodan search "apache city:\"New York\""
shodan search "port:3389 country:US"  # RDP
shodan search "port:27017"            # MongoDB (often exposed)

# Key filters
# org:        — Organization name
# port:       — Specific port
# city:       — City location
# country:    — Country code
# product:    — Software product
# has_screenshot:true — Devices with web interfaces
```

Shodan reveals operating systems, software versions, open ports, SSL certificates, and sometimes even default credentials left on IoT devices.

**Censys** and **ZoomEye** are alternatives with different indexing strengths. BinaryEdge excels at finding exposed databases.

### WHOIS & Historical Data

WHOIS records show domain registration details. Post-GDPR, most records are redacted — but historical WHOIS data (via DomainTools, WhoisXMLAPI) often reveals the registrant's name, email, and organization from before GDPR enforcement.

```bash
whois <target_domain>
# Look for: Registrant Name, Email, Organization, Creation Date
```

---

## Phase 4: Document & Metadata Forensics

Every file carries hidden metadata — who created it, when, with what software, and sometimes *where*.

### EXIF Data Extraction

Photos taken with smartphones often embed GPS coordinates, device model, timestamp, and camera settings directly into the file.

```bash
# Install ExifTool
sudo apt install libimage-exiftool-perl

# Extract all metadata from an image
exiftool <image.jpg>

# Key fields to look for:
# GPS Latitude:  40 deg 45' 28.80" N
# GPS Longitude: 73 deg 59' 07.80" W
# → Plug into Google Maps → Times Square, NYC

# Make/Model: Apple iPhone 14 Pro
# Date/Time Original: 2024:01:15 14:30:00
# Software: sometimes reveals editing tools
```

**Red flags in metadata:** If the camera model says "iPhone 14 Pro" but the software says "Nikon Transfer" — the metadata has been manipulated. In OSINT challenges and real investigations, this is common and tells you the data may have been injected from a different source.

### PDF Metadata

PDFs embed author names, creation tools, organization names, and sometimes internal file paths:

```bash
exiftool document.pdf

# Look for:
# Author: Jennifer Martinez
# Creator: Microsoft Word 2019
# Producer: Adobe PDF Library 15.0
# Create Date: 2024:03:15
```

The author field is often the creator's real name or their Windows username — a direct identity indicator.

### Image Geolocation Without GPS

When EXIF GPS data has been stripped, visual analysis becomes the technique:

- **Landmark identification** — buildings, monuments, signage
- **Language on signs** — narrows to country/region
- **Sun position** — combined with date, narrows to hemisphere and latitude
- **Vegetation** — climate indicators
- **Vehicle license plates** — country/state identification
- **Power line configuration** — differs by country

Google Lens and Yandex Reverse Image Search are the primary tools for visual geolocation when metadata is unavailable.

---

## Phase 5: Credential Leak & Breach Discovery

Breached databases are the dark matter of OSINT — massive datasets of usernames, passwords, and personal information from compromised services. Finding a target's credentials in a breach reveals password patterns, associated emails, and often real identity.

### Search Techniques

```bash
# Google Dork for paste sites
site:pastebin.com "<target_email>"
site:pastebin.com "<target_username>"

# Search multiple paste platforms
"<target_email>" site:ghostbin.co OR site:controlc.com OR site:rentry.co

# HudsonRock Cavalier (API-based breach search)
curl "https://cavalier.hudsonrock.com/api/json/v2/osint-tools/search-by-username?username=<target>"
```

### Breach Intelligence Platforms

- **Have I Been Pwned** — checks if an email appears in known breaches
- **DeHashed** — searchable breach database (email, username, IP, name, phone)
- **IntelX** — intelligence search engine indexing paste sites, breach data, dark web

### What Leaked Credentials Reveal

- **Password patterns** — if a target uses `Company2024!` in one breach, they likely use `Company2025!` elsewhere
- **Email variations** — secondary accounts, personal vs. work email
- **Associated services** — what platforms they've registered on
- **Password reuse** — same credentials across multiple services

---

## Phase 6: Cryptocurrency & Financial Tracing

Blockchain transactions are permanent, public, and traceable. Every Bitcoin, Ethereum, and most cryptocurrency transactions are recorded on an immutable ledger that anyone can query.

### Blockchain Exploration

```bash
# Bitcoin — check address transactions
# Use blockchain.com/explorer or blockchair.com
# Enter wallet address → see all incoming/outgoing transactions

# Ethereum — Etherscan
# etherscan.io/address/<wallet_address>
# Shows token transfers, contract interactions, connected addresses
```

### Tracing Methodology

1. **Identify the wallet** — from breach data, dark web posts, or ransom demands
2. **Map transaction graph** — follow money flow through intermediate wallets
3. **Identify exchanges** — transactions to known exchange addresses (Binance, Coinbase) may be linked to KYC'd accounts
4. **Cluster analysis** — multiple addresses controlled by the same entity often transact with each other
5. **Timing analysis** — transaction timestamps correlate with timezone and activity patterns

**Chainalysis** and **Elliptic** are commercial tools used by law enforcement for advanced tracing. For OSINT, **Blockchair** and **OXT.me** provide powerful free exploration.

---

## Phase 7: Intelligence Production & Reporting

Raw data isn't intelligence. Intelligence is data that has been collected, processed, analyzed, and presented in a format that enables decision-making.

### The Intelligence Cycle

```
Collection → Processing → Analysis → Dissemination → Feedback
     ↑                                                    │
     └────────────────────────────────────────────────────┘
```

### Analysis Frameworks

**Analysis of Competing Hypotheses (ACH):** List all possible explanations. Map every piece of evidence against every hypothesis. Actively try to disprove your leading theory. The hypothesis that survives disproval attempts is the strongest.

**Diamond Model:** Connects four elements of every intrusion — Adversary, Capability, Infrastructure, and Victim. Every OSINT finding maps to at least one of these elements.

### Avoiding Confirmation Bias

The single most dangerous mistake in OSINT is confirmation bias — finding what you expect to find instead of what's actually there. Countermeasures:

- Always maintain at least 2 competing hypotheses
- Actively search for evidence that contradicts your leading theory
- Document what you *didn't* find, not just what you did
- Have a second analyst review your conclusions independently

### Visualization

**Maltego** graphs for relationship mapping. **Timeline tools** (TimelineJS) for chronological event mapping. **Geographic tools** for plotting physical locations. The finished intelligence product should tell a story that a non-technical decision-maker can follow.

---

## The OSINT Toolbox — Quick Reference

| Category | Tool | Purpose |
|---|---|---|
| **Username Search** | Sherlock, Maigret | Cross-platform account discovery |
| **Automated OSINT** | SpiderFoot, Recon-ng | Multi-source automated collection |
| **Link Analysis** | Maltego | Visual relationship mapping |
| **DNS Recon** | dig, nslookup, DNSRecon | Domain infrastructure mapping |
| **CT Logs** | crt.sh, Certspotter | Subdomain discovery via certificates |
| **Device Search** | Shodan, Censys, ZoomEye | Internet-connected device indexing |
| **WHOIS** | whois, DomainTools | Domain registration data |
| **Metadata** | ExifTool | Image/document metadata extraction |
| **Geolocation** | Google Earth, GeoGuessr | Visual location analysis |
| **Email Analysis** | MXToolbox, header analyzers | Email header forensics |
| **Breach Search** | HIBP, DeHashed, IntelX | Credential leak discovery |
| **Blockchain** | Blockchair, Etherscan, OXT | Cryptocurrency transaction tracing |
| **Google Dorks** | Google, DuckDuckGo | Advanced search operator queries |
| **Social Media** | TweetDeck, Twint, Instaloader | Platform-specific collection |
| **Dark Web** | Tor, Ahmia, OnionScan | .onion site indexing |
| **OPSEC** | Tor Browser, Mullvad, VMs | Investigator identity protection |

---

## Mapping OSINT to MITRE ATT&CK

| OSINT Phase | ATT&CK Technique | ID |
|---|---|---|
| Target ID / Username Correlation | Gather Victim Identity Information | T1589 |
| Digital Footprint Mapping | Search Open Websites/Domains | T1593 |
| Infrastructure Recon | Active Scanning | T1595 |
| DNS / WHOIS | Gather Victim Network Information | T1590 |
| Document & Metadata Forensics | Gather Victim Host Information | T1592 |
| Credential Leak Discovery | Gather Victim Identity: Credentials | T1589.001 |
| Social Engineering Prep | Phishing for Information | T1598 |
| Search Engines / Dorking | Search Open Technical Databases | T1596 |

---

## Legal & Ethical Considerations

OSINT operates in public data — but "public" doesn't mean "unrestricted."

**GDPR** (General Data Protection Regulation) requires a lawful basis for processing personal data, even when publicly available. The three most relevant bases for OSINT: Legitimate Interests, Public Task (law enforcement), and Legal Obligation (AML/KYC).

**Key GDPR principles for investigators:**
- **Purpose limitation** — data collected for an investigation cannot be repurposed for marketing
- **Data minimization** — collect only what the investigation requires
- **Storage limitation** — delete data when the investigation closes

**CFAA** (Computer Fraud and Abuse Act, US) — accessing systems without authorization is illegal, even if "the door was open." OSINT stays passive — you read what's publicly available, you don't exploit, scan, or access restricted systems.

**The Golden Rule:** If you had to click "I agree" to see it, or if a login wall blocked it, it's not OSINT — it's unauthorized access.

---

## Real-World OSINT in Action

### MGM Resorts (2023) — Social Engineering Enabled by OSINT

Scattered Spider's attack on MGM started with LinkedIn OSINT. They identified IT help desk employees by name, role, and reporting structure — all from public profiles. Armed with this intelligence, they made a single vishing call impersonating a legitimate employee, got MFA reset, and walked into Okta. $100M later, the organization was still recovering.

The OSINT phase took hours. The impact lasted months.

### Bellingcat — Geolocation Intelligence

Bellingcat's investigative journalists have used OSINT geolocation techniques — shadow analysis, landmark identification, vegetation patterns, and satellite imagery cross-referencing — to verify conflict zone footage, identify military unit movements, and attribute chemical weapons attacks. Their work demonstrates that OSINT isn't just a red team tool — it's an investigative discipline.

---

## Closing

OSINT is the first phase of every serious operation — offensive or defensive. Red teamers use it to build target profiles. Threat intelligence analysts use it to track adversary infrastructure. Blue teamers use it to understand their own attack surface.

The techniques in this post aren't theoretical. Every tool has a command. Every command produces output you can pivot from. The framework scales from a single username to a full organizational assessment.

The information is already out there. The only question is whether you find it first — or they do.

---

**Subscribe:** [YouTube](https://youtube.com/@negansec) for OSINT walkthroughs and tool demos.

**Next up:**  
→ *AI Architecture Attacks* — data poisoning, model manipulation, and LLM exploitation from the adversary's perspective

---

## References

1. **MITRE ATT&CK — Reconnaissance Tactics** — [attack.mitre.org/tactics/TA0043](https://attack.mitre.org/tactics/TA0043/)
2. **Sherlock Project** — [github.com/sherlock-project/sherlock](https://github.com/sherlock-project/sherlock)
3. **SpiderFoot** — [github.com/smicallef/spiderfoot](https://github.com/smicallef/spiderfoot)
4. **Shodan** — [shodan.io](https://shodan.io)
5. **crt.sh — Certificate Transparency Search** — [crt.sh](https://crt.sh)
6. **ExifTool by Phil Harvey** — [exiftool.org](https://exiftool.org)
7. **Have I Been Pwned** — [haveibeenpwned.com](https://haveibeenpwned.com)
8. **Bellingcat Online Investigation Toolkit** — [bellingcat.com](https://www.bellingcat.com/)
9. **GDPR — Article 6: Lawfulness of Processing** — [gdpr-info.eu/art-6-gdpr](https://gdpr-info.eu/art-6-gdpr/)
10. **Maltego** — [maltego.com](https://www.maltego.com/)
11. **Optiv — Disrupting the Cyber Kill Chain Using OSINT** — [optiv.com](https://www.optiv.com/insights/discover/blog/leveraging-open-source-intelligence-osint-against-cyber-kill-chain)

---

*— 0xNegan*

[LinkedIn](https://www.linkedin.com/in/ahmed-13b6bb279) · [YouTube](https://youtube.com/@negansec) · [Telegram: Macroc](https://t.me/Macroc) · [Medium](https://medium.com/@CipherHawk/) · [Sherlock](https://audits.sherlock.xyz/watson/0xNegan)
