---
icon: fas fa-trophy
order: 3
title: Hall of Fame
---

## Bug Bounty & Web Security

<div class="stat-grid">
  <div class="stat-card"><span class="stat-number">100+</span><span class="stat-label">Total Vulnerabilities</span></div>
  <div class="stat-card"><span class="stat-number">30+</span><span class="stat-label">Programs Targeted</span></div>
  <div class="stat-card"><span class="stat-number">5+</span><span class="stat-label">Hall of Fame Entries</span></div>
</div>

### Hall of Fame

| Rank | Platform | Key Findings |
|---:|---|---|
| **#1** | **Shadow.pc** | API key → admin telemetry takeover, session management, email bombing, server misconfig, directory disclosure |
| **Top 5** | **BugCrowd BBP** | P1 session re-auth bypass — any valid DB password accesses any account |
| **Top 5** | **GeoComply** | XSS, private key leak on JWKS, admin cred disclosure, email bombing |
| **Top 7** | **LastPass** | Race condition + exposed API key → data manipulation |
| **Ack'd** | **Tesla Motors** | CORS misconfiguration |
| **Recent** | **Hi5 Chatbot** | Prompt injection → URL spoofing → phishing chain |

### Additional Targets

T-Mobile (Open Redirect, XSS+CSRF) · ShutterStock (HTML injection, XSS) · Easyship (IDOR, Race Condition bypassing premium tier, XSS) · Enterprise Management App (JWT `alg:none` → admin access, broken access control) · Finance Platform (ATO via response manipulation) · Russian Medical Platform (ATO via rate limit bypass, Azure subdomain takeover) · Moov Finance (ATO via reset password manipulation) · Chinese Programs (XSS in AI chat, XSS in upload) · YesWeHack (HTML injection)

**Platforms:** Top 700 YesWeHack · Top 700 HackenProof

---

## Smart Contract Security (Web3)

<div class="stat-grid">
  <div class="stat-card"><span class="stat-number">54+</span><span class="stat-label">Confirmed Findings</span></div>
  <div class="stat-card"><span class="stat-number">10+</span><span class="stat-label">High Severity</span></div>
  <div class="stat-card"><span class="stat-number">27</span><span class="stat-label">Single Protocol Record</span></div>
  <div class="stat-card"><span class="stat-number">$60M+</span><span class="stat-label">Prevented Losses</span></div>
</div>

### Platform Rankings

| Platform | Handle | Findings | Highlights |
|---|---|---|---|
| **Sherlock** | [0xNegan](https://audits.sherlock.xyz/watson/0xNegan) | 10H · 28M | 1x 2nd · 2x 3rd · 6x Top 10 |
| **Code4rena** | [Shinobi](https://code4rena.com/@shinobi) | 3H · 7M | Top 230 yearly |
| **CodeHawks** | [CipherHawk](https://profiles.cyfrin.io/u/cipherhawk) | 4H · 16M · 13L | **#41** (12mo) · #441 all-time |
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
| **CyShield CyCTF 2024** | **#1 qualifications (6 consecutive hours).** Led R3dNexus — 3 members assembled one day prior. First CTF ever. Top 20 overall national. |

---

## Leadership & Community

- Led **3 international teams** across Lebanon, Singapore, India, South Africa, London, Japan, Saudi Arabia
- Mentored **2,000+ professionals** via LinkedIn/Medium on web security, API pentesting, smart contract auditing
- Collaborated with **Odyssey** (Top 1 Egypt, HackerOne) on pentesting framework development
- Secured foreign startup applications through volunteer security assessments and professional disclosure
