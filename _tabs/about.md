---
icon: fas fa-skull-crossbones
order: 5
---

> *"I'm gonna make you a promise right here. You work with me... and you will not die."*  
> That's the offer I make to every protocol, every network, every organization I test. Work with me — let me find it first — and the real adversaries won't get the chance.

---

## `$ whoami`

**0xNegan.** Red Team Operator. Cloud Security Researcher. Web3 Auditor. AI Red Teamer.

100+ vulnerabilities across 30+ bug bounty programs. 54+ confirmed findings across 10+ DeFi protocols — $60M+ in prevented losses. 5+ Hall of Fame recognitions including #1 on Shadow.pc. Led 3 international offensive teams across 7 countries. Mentored 2,000+ security professionals.

BSc in Artificial Intelligence (Second Upper Class Honours). Built **CipherSight** — an AI-powered pentesting framework with fine-tuned RoBERTa for CVE classification and automated remediation.

I went from first vulnerability to 6 confirmed findings in 13 days. Four months later: Hall of Fame on LastPass, BugCrowd, Shadow.pc, and GeoComply. The velocity hasn't slowed down.

---

## `$ cat /etc/0xnegan/stats`

<div class="stat-grid">
  <div class="stat-card"><span class="stat-number">100+</span><span class="stat-label">Vulnerabilities Discovered</span></div>
  <div class="stat-card"><span class="stat-number">5+</span><span class="stat-label">Hall of Fame</span></div>
  <div class="stat-card"><span class="stat-number">#1</span><span class="stat-label">CyShield CTF 2024</span></div>
  <div class="stat-card"><span class="stat-number">$60M+</span><span class="stat-label">Prevented Losses</span></div>
  <div class="stat-card"><span class="stat-number">54+</span><span class="stat-label">Web3 Findings</span></div>
  <div class="stat-card"><span class="stat-number">27</span><span class="stat-label">Single Protocol</span></div>
</div>

---

## `$ cat certifications.txt`

<span class="cert-badge">CRTO — Certified Red Team Operator</span>
<span class="cert-badge">COPO — Certified Offensive Phishing Operator</span>
<span class="cert-badge">CORA — OSINT Research Analyst</span>
<span class="cert-badge">HTB AI Red Teamer (in progress)</span>

**Training:** OSCP content (RedNexus) · eWAPT/eWAPTx · PortSwigger Academy (all labs) · Hack The Box (most labs) · MITRE ATT&CK · PTES · Programming Hub (Google Partner)

---

## `$ ls hall-of-fame/`

### Web Application & API Security

| Rank | Target | Finding |
|---:|---|---|
| **#1** | **Shadow.pc** | Exposed API key → admin telemetry takeover + internal DoS. Improper session management in password change. Email bombing via rate limit bypass. Server misconfiguration allowing backend manipulation. Directory brute-force information disclosure. |
| **Top 5** | **BugCrowd BBP** | P1 — Session re-authentication bypass: any valid password in the database could authenticate any account. |
| **Top 5** | **GeoComply** | Reflected XSS. Private key leak on JWKS endpoint. Admin credential disclosure via directory enumeration. Email bombing. |
| **Top 7** | **LastPass** | Race condition in community section. Exposed API key → unauthorized data manipulation. |
| **Ack'd** | **Tesla Motors** | CORS misconfiguration enabling cross-origin attacks. |
| **Recent** | **Hi5 Chatbot** | Prompt injection → URL spoofing → phishing chain on major platform. |

**Additional targets:** T-Mobile (Open Redirect, XSS+CSRF chain) · ShutterStock (HTML injection, XSS) · Easyship (IDOR, Race Condition bypassing premium plan) · Enterprise Management App (JWT `alg: none` → admin access, broken access control) · Finance Platform (ATO via password reset response manipulation) · Top 700 YesWeHack · Top 700 HackenProof

### Smart Contract Security (Web3)

| Platform | Handle | Findings | Rank |
|---|---|---:|---|
| **Sherlock** | [0xNegan](https://audits.sherlock.xyz/watson/0xNegan) | 10 High · 28 Medium | 27x payouts · 1x 2nd · 2x 3rd · 6x Top 10 |
| **Code4rena** | [Shinobi](https://code4rena.com/@shinobi) | 3 High · 7 Medium | #230 yearly · $994 |
| **CodeHawks** | [CipherHawk](https://profiles.cyfrin.io/u/cipherhawk) | 4 High · 16 Med · 13 Low | #41 (12mo) · #441 all-time |
| **Cantina** | RektOracle | 10 findings | $156 |

**Notable protocol wins:**

| Protocol | Result | Severity |
|---|---|---|
| RAAC Core Contracts | **27 vulnerabilities** in single audit · #102 leaderboard · 617 XP | 3H / 14M / 10L |
| Ethos Reputation Market | **Top 2** auditor | Medium |
| Flex Perpetuals | **#4 rank** | Medium |
| Autonomint Colored Dollar | DoS via `updateDownsideProtected()` | **High** |
| LEND | Liquidation finalization failure — mismatched token/chain | **High** |
| Symmio Staking & Vesting | Precision loss in reward calculations | **High** |
| SecondSwap | Users claim more than actual allotment | **High** |
| Liquidity Management | Wrong `refundExecutionFee` in `_handleReturn` | **High** |
| Forte Float128 | `ln()` silently accepts invalid non-positive inputs | **High** |
| Mystic Finance | Accounting flaw → potential insolvency | **High** |

### Competition

| Event | Result |
|---|---|
| **CyShield CyCTF 2024** | #1 in qualifications (6 hours). Led Team R3dNexus — 3 members. First CTF ever. Top 20 overall. |

---

## `$ ls published-work/`

### Network Penetration Testing — Complete Methodology
200+ saves · 7,000+ visits · Community-adopted reference  
[**View on Notion →**](https://spangle-snail-0fe.notion.site/Network-Penetration-Testing-Complete-Methodology-48b89f2cef9d41c1a7988acdd8c8a0fb)

### Red Team Operations & Adversary Simulation Framework
Full kill-chain methodology aligned with CRTO tradecraft  
[**View on Notion →**](https://chemical-azimuth-aa6.notion.site/Red-Team-Operations-Adversary-Simulation-2e45fe61924981098a08c710b06fa8c1)

### CipherSight AI Security Framework
Graduation project — automated pentesting with AI-powered CVE classification (RoBERTa fine-tuned on NVD 2016-2023) and remediation engine.

---

## `$ cat /etc/0xnegan/focus-areas`

**Offensive Cloud Security** — Identity attacks, device code phishing, consent grant manipulation, MFA downgrade, token abuse across Azure/AWS/GCP.

**Red Team Operations** — Adversary simulation, modular infrastructure, OPSEC-hardened phishing, C2 deployment, Active Directory exploitation.

**AI Red Teaming** — Adversarial ML, LLM exploitation, prompt injection, data poisoning, AI-powered social engineering.

**Web3 Security** — Solidity/EVM smart contract auditing, DeFi vulnerability research, competitive audit competitions.

**OSINT** — Digital investigations, credential leak discovery, blockchain analysis, threat intelligence.

---

## `$ cat /etc/0xnegan/leadership`

Led **3 international teams** across MEA, Europe, Singapore, India, South Africa, Japan, Saudi Arabia. Personally handled test planning, attack simulation, PoC development, and reporting.

Mentored **2,000+ professionals** on web security, API pentesting, and smart contract auditing via LinkedIn and Medium.

Collaborated with **Odyssey** (Top 1 Egypt on HackerOne) on pentesting framework development.

---

## `$ cat contact.json`

```json
{
  "linkedin":  "linkedin.com/in/ahmed-13b6bb279",
  "youtube":   "@negansec",
  "telegram":  "Macroc",
  "discord":   "#PCProdigy5311",
  "medium":    "@CipherHawk",
  "github":    "0xnegan",
  "sherlock":  "audits.sherlock.xyz/watson/0xNegan",
  "code4rena": "code4rena.com/@shinobi",
  "codehawks": "profiles.cyfrin.io/u/cipherhawk"
}
```
