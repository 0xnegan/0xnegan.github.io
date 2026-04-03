---
icon: fas fa-skull-crossbones
order: 5
---

> *"I'm gonna make you a promise right here. You work with me... and you will not die."*  
> That's the offer to every protocol, every network, every organization I test. Let me find it first — and the real adversaries won't get the chance.

---

## `$ whoami`

**0xNegan.** Red Team Operator. Cloud Security Researcher. Web3 Auditor. AI Red Teamer. AI Engineer.

100+ vulnerabilities across 30+ bug bounty programs. 54+ confirmed smart contract findings across 10+ DeFi protocols — preventing potential losses exceeding $60M. 5+ Hall of Fame recognitions. Led 3 international offensive security teams across 7 countries. Mentored 2,000+ security professionals.

Built **CipherSight** — an AI-powered automated pentesting framework integrating multi-tool reconnaissance, Nuclei vulnerability detection, fine-tuned RoBERTa model for CVE classification (trained on NVD data 2016-2023), and an AI-driven remediation engine.

First vulnerability to 6 confirmed findings in 13 days. Four months in: Hall of Fame on LastPass, BugCrowd, Shadow.pc, and GeoComply. The velocity hasn't slowed.

---

## `$ cat /etc/0xnegan/stats`

<div class="stat-grid">
  <div class="stat-card"><span class="stat-number">100+</span><span class="stat-label">Vulnerabilities Discovered</span></div>
  <div class="stat-card"><span class="stat-number">5+</span><span class="stat-label">Hall of Fame</span></div>
  <div class="stat-card"><span class="stat-number">#1</span><span class="stat-label">CyShield CTF 2024</span></div>
  <div class="stat-card"><span class="stat-number">$60M+</span><span class="stat-label">Prevented Losses</span></div>
  <div class="stat-card"><span class="stat-number">54+</span><span class="stat-label">Web3 Findings</span></div>
  <div class="stat-card"><span class="stat-number">27</span><span class="stat-label">Single Protocol Record</span></div>
  <div class="stat-card"><span class="stat-number">2K+</span><span class="stat-label">Community Mentored</span></div>
  <div class="stat-card"><span class="stat-number">3</span><span class="stat-label">International Teams Led</span></div>
</div>

---

## `$ cat certifications.txt`

<span class="cert-badge">CRTO — Certified Red Team Operator</span>
<span class="cert-badge">COPO — Certified Offensive Phishing Operator</span>
<span class="cert-badge">CORA — OSINT Research Analyst</span>
<span class="cert-badge">HTB AI Red Teamer (in progress)</span>

**Training:** OSCP content (RedNexus) · eWAPT/eWAPTx · PortSwigger Academy (all labs) · Hack The Box (most labs) · MITRE ATT&CK · PTES · Programming Hub (Google Partner) — Hacking, Advanced Hacking, Security Fundamentals

---

## `$ ls hall-of-fame/`

### Web Application & API Security

| Rank | Target | Critical Findings |
|---:|---|---|
| **#1** | **Shadow.pc** | Exposed API key → admin telemetry takeover + internal DoS. Improper session management on password change. Email bombing via missing rate limiting. Server misconfiguration — frontend-only validation bypass via Burp. Directory brute-force information disclosure. |
| **Top 5** | **BugCrowd BBP** | P1 — Session re-authentication bypass: any valid password in the database could access any account session. |
| **Top 5** | **GeoComply** | Reflected XSS. Private key leak on JWKS endpoint response. Admin credentials exposed via directory enumeration. Email bombing on `/resetpassword`. |
| **Top 7** | **LastPass** | Race condition in community section allowing duplicate entries. Exposed API key → unauthorized data manipulation. |
| **Ack'd** | **Tesla Motors** | CORS misconfiguration enabling cross-origin attacks. |
| **Recent** | **Hi5 Chatbot** | Prompt injection → URL spoofing → phishing on major platform with AI chatbot agent. |

**Additional programs and findings:**

| Target | Findings |
|---|---|
| **T-Mobile** | Open Redirect. Reflected XSS chained with CSRF on API. |
| **ShutterStock** | HTML injection in developer support portal. Reflected XSS in comment section. |
| **Easyship** | IDOR → unauthorized data tampering. Race Condition bypassing premium plan from basic. Reflected XSS in unsanitized parameter. |
| **Enterprise Management App** | JWT algorithm set to `none` → signature removal → admin privilege escalation. Broken access control on deletion function → vertical privilege escalation. Database information leak in JS files. |
| **Finance Platform** | ATO from password reset response manipulation. |
| **Russian Medical Platform** | Account takeover via missing rate limiting on `/hub/login`. Azure subdomain takeover. |
| **Chinese Programs** | Reflected XSS in AI assistant chat. Reflected XSS in upload function in support chat. |
| **Moov Finance** | Improper authentication — ATO using response manipulation on reset password + email change. |
| **YesWeHack Program** | HTML injection. |

**Platform rankings:** Top 700 YesWeHack · Top 700 HackenProof

---

### Smart Contract Security (Web3)

Securing billion-dollar DeFi protocols across four major competitive audit platforms. 54+ confirmed vulnerabilities. 27 vulnerabilities in a single protocol (RAAC Core Contracts). First to secure billion-dollar valuation protocols, preventing $60M+ in potential losses.

| Platform | Handle | Findings | Rank Highlights |
|---|---|---|---|
| **Sherlock** | [0xNegan](https://audits.sherlock.xyz/watson/0xNegan) | 10 High · 28 Medium | 1x 2nd place · 2x 3rd place · 6x Top 10 |
| **Code4rena** | [Shinobi](https://code4rena.com/@shinobi) | 3 High · 7 Medium | #230 yearly rank |
| **CodeHawks** | [CipherHawk](https://profiles.cyfrin.io/u/cipherhawk) | 4 High · 16 Med · 13 Low | **#41 rank** (12 months) · #441 all-time |
| **Cantina** | RektOracle | 10 findings | Active researcher |

**Protocol wins with severity breakdown:**

| Protocol | Result | Severity | Finding |
|---|---|---|---|
| **RAAC Core Contracts** | **27 vulns** in single audit · #102 leaderboard | 3H/14M/10L | BaseGauge rewards without staking, GaugeController fee loss, voting power snapshot missing, timelock bypass, supply cap bypass, and 22 more |
| **Ethos Reputation Market** | **Top 2** auditor | Medium | Rounding arbitrage — different rounding for Trust vs. Distrust |
| **Flex Perpetuals** | **#4 rank** | Medium | Missing slippage protection in AerodromeDexter |
| **Autonomint Colored Dollar** | 2 Highs | **High** | `updateDownsideProtected()` DoS + manipulation |
| **LEND** | #57 | **High** | Liquidation finalization fails — mismatched token/chain contexts |
| **Symmio Staking & Vesting** | #11 | **High** | Precision loss in reward calculations undermines user rewards |
| **SecondSwap** | 1 High + 2 Medium | **High** | Users claim more than actual allotment |
| **Liquidity Management** | 1 High + 2 Medium | **High** | Wrong `refundExecutionFee` in `_handleReturn` |
| **Forte Float128** | #29 | **High** | Natural logarithm silently accepts invalid non-positive inputs |
| **Mystic Finance** | — | **High** | Accounting flaw in `stPlumeMinter.withdraw` → potential insolvency |
| **Virtuals Protocol** | #22 | Medium | Missing slippage protection + ELO manipulation via `_castVote` |
| **Alchemix Transmuter** | #26 | Medium | Claimable balance missing from `_harvestAndReport` total assets |
| **Rova** | — | Medium | Unit mismatch in participation updates |
| **Yieldoor** | #28 | Medium | Locked funds due to underflow in withdrawal |
| **Aave v3.3** | Multiple | — | Submissions across Sherlock |
| **IQ AI** | #16 | Medium | Ineffective proposal threshold validation |
| **Liquid Ron** | #11 | Medium | Incorrect `onlyOperator` modifier logic → DoS |

---

### Competition

| Event | Result |
|---|---|
| **CyShield CyCTF 2024** | **#1 in qualifications for 6 consecutive hours.** Led Team R3dNexus — 3 members assembled one day before competition. First CTF ever competed in. Top 20 overall finish in national competition. |

---

## `$ ls published-work/`

### Network Penetration Testing — Complete Methodology
200+ saves · 7,000+ visits · Community-adopted reference  
[**View on Notion →**](https://spangle-snail-0fe.notion.site/Network-Penetration-Testing-Complete-Methodology-48b89f2cef9d41c1a7988acdd8c8a0fb)

### Red Team Operations & Adversary Simulation Framework
Full kill-chain adversary simulation methodology aligned with CRTO tradecraft  
[**View on Notion →**](https://chemical-azimuth-aa6.notion.site/Red-Team-Operations-Adversary-Simulation-2e45fe61924981098a08c710b06fa8c1)

### CipherSight AI Security Framework
Automated pentesting framework integrating multi-tool reconnaissance → fingerprinting → alive subdomain filtering → endpoint discovery → Nuclei vulnerability detection → fine-tuned RoBERTa model on NVD CVE data (2016-2023) for accurate classification → AI-powered reporting engine generating executive summaries with automated remediation strategies.

---

## `$ cat /etc/0xnegan/focus-areas`

**Offensive Cloud Security** — Identity attacks, device code phishing, consent grant manipulation, MFA downgrade, token abuse across Azure/AWS/GCP.

**Red Team Operations** — Adversary simulation, modular infrastructure, OPSEC-hardened phishing, C2 deployment, Active Directory exploitation (BloodHound, Mimikatz, Rubeus, ADCS, Kerberoasting, DCSync, token manipulation).

**AI Red Teaming** — Adversarial ML, LLM exploitation, prompt injection, data poisoning, AI-powered social engineering, deepfake-enhanced attack chains.

**Web3 Security** — Solidity/EVM smart contract auditing, DeFi vulnerability research, competitive audit competitions across Sherlock, Code4rena, CodeHawks, and Cantina.

**OSINT & Digital Investigations** — Open-source intelligence, credential leak discovery, blockchain analysis, threat intelligence, OPSEC.

---

## `$ cat /etc/0xnegan/leadership`

Led **3 international offensive security teams** — members across Lebanon, Singapore, India, South Africa, London, Japan, and Saudi Arabia. Personally handled test planning, attack simulation, execution, PoC development, and report writing.

Mentored **2,000+ security professionals** across LinkedIn and Medium on web application security, API penetration testing, and smart contract auditing journey — from first bug to Hall of Fame.

Collaborated with **Odyssey** (Top 1 team in Egypt on HackerOne) on pentesting framework development.

Volunteered in securing foreign startups — identified and documented vulnerabilities with professional disclosure, helping gaming communities maintain secure e-commerce operations.

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
  "codehawks": "profiles.cyfrin.io/u/cipherhawk",
  "cantina":   "cantina.xyz — @RektOracle"
}
```
