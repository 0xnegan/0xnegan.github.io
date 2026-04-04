---
icon: fas fa-trophy
order: 3
title: Hall of Fame
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

## Web Application & API Security

### Hall of Fame

| Rank | Target | Critical Findings |
|---:|---|---|
| **#1** | **Shadow.pc** | Exposed API key → admin telemetry takeover + internal DoS. Improper session management on password change. Email bombing via missing rate limiting. Server misconfiguration — frontend-only validation bypass via Burp. Directory brute-force information disclosure. |
| **Top 5** | **BugCrowd BBP** | P1 — Session re-authentication bypass: any valid password in the database could access any account session. |
| **Top 5** | **GeoComply** | Reflected XSS. Private key leak on JWKS endpoint response. Admin credentials exposed via directory enumeration. Email bombing on `/resetpassword`. |
| **Top 7** | **LastPass** | Race condition in community section allowing duplicate entries. Exposed API key → unauthorized data manipulation. |
| **Ack'd** | **Tesla Motors** | CORS misconfiguration enabling cross-origin attacks. |
| **Recent** | **Hi5 Chatbot** | Prompt injection → URL spoofing → phishing on major platform with AI chatbot agent. |

### Additional Programs

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

## Smart Contract Security (Web3)

54+ confirmed vulnerabilities across 10+ DeFi and infrastructure protocols. 27 vulnerabilities in a single protocol (RAAC Core Contracts). First to secure billion-dollar valuation protocols, preventing $60M+ in potential losses. Senior Auditor and Judge status on Sherlock.

### Platform Rankings

| Platform | Handle | Findings | Highlights |
|---|---|---|---|
| **Sherlock** | [0xNegan](https://audits.sherlock.xyz/watson/0xNegan) | 10 High · 28 Medium | 1x 2nd · 2x 3rd · 6x Top 10 |
| **Code4rena** | [Shinobi](https://code4rena.com/@shinobi) | 3 High · 7 Medium | Top 230 yearly |
| **CodeHawks** | [CipherHawk](https://profiles.cyfrin.io/u/cipherhawk) | 4 High · 16M · 13 Low | **#41** (12mo) · #441 all-time |
| **Cantina** | RektOracle | 10 findings | Active |

### Protocol Wins

| Protocol | Rank / Result | Severity | Finding |
|---|---|---|---|
| **RAAC Core Contracts** | 27 vulns · #102 | 3H/14M/10L | BaseGauge rewards without staking, GaugeController fee loss, voting power snapshot, timelock bypass, MAX_TOTAL_SUPPLY bypass, governance manipulation, +21 more |
| **Ethos Reputation Market** | **Top 2** | Medium | Rounding arbitrage between Trust vs. Distrust |
| **Flex Perpetuals** | **#4** | Medium | Missing slippage in AerodromeDexter |
| **Autonomint Colored Dollar** | — | **2x High** | DoS via `updateDownsideProtected()` + manipulation |
| **LEND** | #57 | **High** | Liquidation fails — mismatched token/chain |
| **Symmio Staking** | #11 | **High** | Precision loss in reward calculations |
| **SecondSwap** | — | **High** | Users claim beyond allotment |
| **Liquidity Management** | — | **High** | Wrong `refundExecutionFee` |
| **Forte Float128** | #29 | **High** | `ln()` accepts invalid inputs silently |
| **Mystic Finance** | — | **High** | Accounting flaw → insolvency risk |
| **Virtuals Protocol** | #22 | Medium | Missing slippage + ELO manipulation |
| **Alchemix Transmuter** | #26 | Medium | Missing claimable balance in total assets |
| **Rova** | — | Medium | Unit mismatch in participation updates |
| **Yieldoor** | #28 | Medium | Underflow in withdrawal → locked funds |
| **IQ AI** | #16 | Medium | Proposal threshold validation bypass |
| **Liquid Ron** | #11 | Medium | `onlyOperator` modifier DoS |
| **Aave v3.3** | — | Multiple | Submissions across Sherlock |

---

## Competition

| Event | Result |
|---|---|
| **CyShield CyCTF 2024** | **#1 in qualifications for 6 consecutive hours.** Led Team R3dNexus — 3 members assembled one day prior. First CTF ever. Top 20 overall in national competition. |

---

## Leadership & Community

- Led **3 international offensive security teams** — members across Lebanon, Singapore, India, South Africa, London, Japan, Saudi Arabia. Personally handled test planning, attack simulation, execution, PoC development, and reporting.
- Mentored **2,000+ security professionals** via LinkedIn and Medium on web application security, API penetration testing, and smart contract auditing.
- Volunteered in securing foreign startup applications through professional disclosure — documenting vulnerabilities and mitigations for both technical and non-technical audiences.
