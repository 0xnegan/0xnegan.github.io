---
icon: fas fa-trophy
order: 3
title: Hall of Fame
---

> *"I am everywhere. And this was just your one chance. And I gave it to you. And you blew it."*

---

## Bug Bounty & Vulnerability Research

<div class="stat-grid">
  <div class="stat-card">
    <span class="stat-number">100+</span>
    <span class="stat-label">Total Vulnerabilities</span>
  </div>
  <div class="stat-card">
    <span class="stat-number">30+</span>
    <span class="stat-label">Programs Targeted</span>
  </div>
  <div class="stat-card">
    <span class="stat-number">5+</span>
    <span class="stat-label">Hall of Fame Entries</span>
  </div>
</div>

### Hall of Fame Recognition

| Platform | Rank | Critical Finding |
|---|---|---|
| **Shadow.pc** | **#1** | API key exposure → admin telemetry access + internal DoS, session management flaws, email bombing, directory disclosure |
| **BugCrowd BBP** | **Top 5** | P1 — Session re-authentication bypass: access any account with any valid password from the database |
| **GeoComply** | **Top 5** | Reflected XSS, private key leak on JWKS, admin credential disclosure via directory enumeration, email bombing |
| **LastPass** | **Top 7** | Race condition in community section + exposed API key → unauthorized data manipulation |
| **Tesla Motors** | **Acknowledged** | CORS misconfiguration enabling cross-origin attacks |
| **Hi5 Chatbot** | **Recent** | Prompt injection → URL spoofing → phishing chain on major platform |

### Additional Targets

T-Mobile (Open Redirect, XSS+CSRF chain), ShutterStock (HTML injection, Reflected XSS), Easyship (IDOR → unauthorized data tampering, Race Condition bypassing premium tier), Major E-commerce (merchant ID leak via error response → API enumeration), Enterprise Management App (JWT algorithm manipulation `none` → admin access, broken access control in deletion), Finance Platform (ATO via password reset response manipulation), Russian Medical Platform (account takeover via login endpoint enumeration), multiple Chinese programs (XSS in AI assistant chat).

**Platform Rankings:** Top 700 YesWeHack · Top 700 HackenProof

---

## Web3 Security Auditing

<div class="stat-grid">
  <div class="stat-card">
    <span class="stat-number">54+</span>
    <span class="stat-label">Confirmed Findings</span>
  </div>
  <div class="stat-card">
    <span class="stat-number">10+</span>
    <span class="stat-label">High Severity</span>
  </div>
  <div class="stat-card">
    <span class="stat-number">27</span>
    <span class="stat-label">Single Protocol Record</span>
  </div>
  <div class="stat-card">
    <span class="stat-number">$60M+</span>
    <span class="stat-label">Prevented Losses</span>
  </div>
</div>

### Platform Rankings

| Platform | Handle | Rank | Findings | Earnings |
|---|---|---|---|---|
| [Sherlock](https://audits.sherlock.xyz/watson/0xNegan) | 0xNegan | 27x payouts, 1x 2nd, 2x 3rd, 6x Top 10 | 10H + 28M | $1.81K |
| [Code4rena](https://code4rena.com/@shinobi) | Shinobi | #230 last year | 3H + 7M | $994 |
| [CodeHawks](https://profiles.cyfrin.io/u/cipherhawk) | CipherHawk | #41 (12mo), #441 all-time | 4H + 16M + 13L | $411 |
| [Cantina](https://cantina.xyz) | RektOracle | Active | 10 findings | $156 |

### Notable Protocol Wins

- **RAAC Core Contracts** — 27 vulnerabilities in one audit (3H, 14M, 10L). #102 on contest leaderboard, 617 XP.
- **Ethos Reputation Market** — Top 2 auditor
- **Flex Perpetuals** — Top 3 (#4 rank)
- **Symmio Staking & Vesting** — #11 rank, High severity precision loss in reward calculations
- **Autonomint Colored Dollar V1** — High severity DoS via `updateDownsideProtected()`
- **LEND** — High severity liquidation finalization failure due to mismatched token/chain contexts
- **Forte Float128** — High severity: natural logarithm silently accepts invalid non-positive inputs
- **SecondSwap** — High severity: users can claim more than actual allotment
- **Liquidity Management** — High severity: wrong `refundExecutionFee` in `_handleReturn`
- **Mystic Finance** — High severity: accounting flaw in `stPlumeMinter.withdraw` leading to insolvency
- **Virtuals Protocol** — Medium severity: missing slippage protection + ELO manipulation via `_castVote`

---

## CTF & Competition

| Event | Result | Detail |
|---|---|---|
| **CyShield CyCTF 2024** | **#1 for qualifications (6 hours)** | Led Team R3dNexus (3 members). First CTF ever. Top 20 overall finish. |

---

## Leadership & Community

- Led **3 international offensive security teams** (MEA, Europe, Singapore, India, South Africa, Japan, Saudi Arabia)
- Mentored **2,000+ security professionals** on web security, API pentesting, and smart contract auditing
- Collaborated with **Odyssey** (Top 1 Egypt on HackerOne) on pentesting framework development
- Active contributor across LinkedIn and Medium — community-adopted methodologies with 200+ saves
