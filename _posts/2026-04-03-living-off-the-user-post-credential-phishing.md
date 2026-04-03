---
title: "Living Off the User — A Red Team Guide to Post-Credential Phishing"
description: "The complete operator's guide to modern identity-layer phishing: ClickFix, Device Code Phishing, MFA Downgrade, Consent Grant Manipulation, and Living-Off-the-User execution across Azure, AWS, GCP, and GitHub. No malware. No exploits. Just trust abuse."
author: 0xnegan
date: 2026-04-03 10:00:00 +0200
categories: [Red Team, Offensive Phishing]
tags: [phishing, red-team, cloud-security, mfa-bypass, opsec, azure, aws, gcp, mitre-attack, identity-attacks, oauth, device-code, consent-grant, clickfix, living-off-the-user]
pin: true
image:
  path: /assets/img/blog/living-off-the-user-banner.png
  alt: "Living Off the User — Post-Credential Phishing Architecture"
---

> *Little pig, little pig... let me in.*

Not by the hair on your chinny chin chin? That's fine. I'm not coming through the door — I'm already past it. I walked in on your session token. I rode in on your OAuth consent. I climbed through the window your clipboard left open when you clicked that CAPTCHA.

That's the thing about modern phishing — the front door isn't the play anymore. Passwords don't get you in. MFA doesn't keep me out. And your FIDO2 security key? I'll make your own login page forget it exists.

This is **Living Off the User** — the field guide to post-credential phishing that doesn't need your password, doesn't need your MFA code, and doesn't drop a single binary on disk. The user becomes the payload. Their own actions become the execution engine. Legitimate system functionality becomes the attack vector.

Lucial is thirsty. Let's feed her.

---

## The Problem: Credentials Aren't Enough Anymore

Here's what nobody tells you in phishing training — stealing a username and password in 2026 gets you almost nothing.

Every mature organization has deployed MFA. Conditional Access policies lock down sign-ins by device, location, and risk score. Token lifetimes are measured in minutes. Session cookies carry device-bound attestation. The traditional phishing playbook — clone a login page, harvest credentials, replay them — hits a wall the moment the authentication flow expects a second factor the attacker doesn't control.

So the game changed. Adversaries stopped targeting *what the user knows* and started targeting *what the user does*.

This shift created two classes of modern phishing techniques that this post breaks down:

**Identity-Layer Attacks** — Techniques that intercept, redirect, or manipulate authentication flows to capture tokens, sessions, and persistent API access. This includes AiTM session hijacking, Device Code Phishing, Consent Grant abuse, and MFA Downgrade tradecraft.

**Living-Off-the-User (LOU) Execution** — Techniques that weaponize the user's own actions against legitimate system functionality. No malware signatures. No exploit code. The user becomes the execution engine — copying commands to their clipboard, pasting into Run dialogs, approving OAuth prompts they don't understand, or clicking through disguised consent flows.

The result? Full account takeover, persistent API access, and arbitrary code execution — all without dropping a single binary on disk.

## The Attack Decision Matrix

Before diving into technique specifics, every operator needs a decision framework. The wrong technique against the wrong target wastes time and burns infrastructure.

```
                    ┌─────────────────────────────┐
                    │   What's the target using?   │
                    └──────────────┬──────────────┘
                                   │
              ┌────────────────────┼────────────────────┐
              ▼                    ▼                    ▼
        ┌───────────┐      ┌───────────┐      ┌──────────────┐
        │ TOTP/SMS/ │      │ FIDO2/    │      │  No MFA /    │
        │   Push    │      │ Passkeys  │      │  Weak Policy │
        └─────┬─────┘      └─────┬─────┘      └──────┬───────┘
              ▼                  ▼                    ▼
        ┌───────────┐    ┌──────────────┐     ┌──────────────┐
        │  AiTM /   │    │ MFA          │     │  Credential  │
        │  Evilginx │    │ Downgrade    │     │  Harvest +   │
        │  Session  │    │ → then AiTM  │     │  Replay      │
        │  Steal    │    │ or DevCode   │     │              │
        └───────────┘    └──────────────┘     └──────────────┘
```

| Target Environment | Primary Technique | Fallback | Why |
|---|---|---|---|
| TOTP / SMS / Push MFA | AiTM (Evilginx) | Device Code Phishing | AiTM captures session cookies post-MFA. Device Code bypasses all MFA types entirely. |
| FIDO2 / Passkeys | MFA Downgrade → AiTM | Device Code Phishing | FIDO2 is domain-bound — can't proxy it. Force the user to a weaker method first. |
| Cloud-heavy (M365, AWS, GCP) | Device Code Phishing | Consent Grant | Device Code gives you tokens without touching MFA. Consent Grant gives persistent API access. |
| Code Execution Required | LOU Techniques | Drive-By Download | ClickFix/FileFix achieve execution via legitimate OS functions. No malware needed. |

---

## Part 1: Living-Off-the-User (LOU) Execution

LOU techniques represent a fundamental shift. Instead of delivering malware that triggers EDR, AMSI, and behavioral analysis, LOU techniques convince the user to execute commands *themselves* using legitimate system functionality.

The user becomes the payload delivery mechanism. No exploit. No dropper. No signature.

### 1.1 ClickFix — Fake CAPTCHA to Clipboard Injection

ClickFix is deceptively simple and devastatingly effective. The victim lands on what appears to be a Cloudflare Turnstile CAPTCHA verification page. When they click the checkbox, JavaScript silently copies a PowerShell command to their clipboard. The page then instructs them to press `Win+R`, paste, and hit Enter.

**Architecture:**

```
┌────────────────────────────────────┐
│         VICTIM'S BROWSER           │
│                                    │
│  ┌──────────────────────────────┐  │
│  │  Fake Cloudflare CAPTCHA     │  │
│  │  ☐ Verify you are human     │──┼──► JS: navigator.clipboard.writeText(PAYLOAD)
│  └──────────────────────────────┘  │
│                                    │
│  "Press Win+R, paste, Enter"       │
│         │                          │
│         ▼                          │
│  ┌──────────────────────────────┐  │
│  │  PowerShell executes         │  │
│  │  attacker's payload          │  │
│  └──────────────────────────────┘  │
└────────────────────────────────────┘
```

**Why it works:** Users are conditioned to complete CAPTCHA challenges without questioning them. The clipboard write is invisible. The payload executes through `Win+R` — a legitimate OS function. No file written to disk. The execution chain is entirely user-driven.

The server implementation is minimal — a Flask app serving a landing page with a blurred document and the CAPTCHA page with clipboard injection logic. The critical JavaScript calls `navigator.clipboard.writeText()` with the encoded payload on checkbox interaction.

**ATT&CK:** T1204.001 (User Execution — Malicious Link)

### 1.2 FileFix — Fake File Share to Explorer Execution

FileFix mimics a file-sharing platform (OneDrive, SharePoint). The victim sees a shared file with a "path" to copy. The "path" is actually a command that executes when pasted into the Windows Explorer address bar — a feature most users don't know about and most security controls don't monitor.

### 1.3 Double Clickjacking — OAuth Consent via UI Overlay

This is the elegant one. Two layers — a visible CAPTCHA overlay and a hidden iframe containing a real OAuth consent prompt. First click repositions the overlay. Second click lands on the hidden "Accept" button.

```
┌─────────────────────────────────────────────────┐
│  VISIBLE LAYER: "Verify you're not a robot"     │
│   Click 1: [ I'm not a robot ]  → moves overlay │
│   Click 2: [ Continue ]         → hits hidden   │
│                                    "Accept"      │
│─────────────────────────────────────────────────│
│  HIDDEN LAYER: Microsoft OAuth Consent Prompt    │
│   "App X wants to: Read mail, Read files..."     │
│   [ Accept ] ← aligned with "Continue" button    │
└─────────────────────────────────────────────────┘
```

Two clicks. That's it. The attacker's OAuth app now has persistent access to the victim's mailbox, files, and identity — indefinitely. Standard phishing training doesn't cover this.

**ATT&CK:** T1098.003 (Additional Cloud Credentials — OAuth App)

### 1.4 Additional LOU Variants

**HtaFix** — HTA files execute via `mshta.exe` with full trust. The user simply opens the file.

**Drive-By Download** — Auto-downloads an executable disguised as legitimate software. The fake page mimics the target org's actual software portal.

**Calendar Invite Injection** — `.ics` files with embedded phishing URLs as meeting join links.

**Invisible Encoding** — Unicode steganography using soft hyphens (`U+00AD`) and zero-width characters (`U+2062`/`U+2064`) to embed hidden payloads in email subjects. Evades signature-based content filtering by breaking string patterns while appearing normal to humans.

---

## Part 2: Identity-Layer Attacks — Where the Real Damage Happens

### 2.1 Device Code Phishing — Bypass ALL MFA Types

This is the single most dangerous phishing technique available today. It bypasses every MFA type — TOTP, SMS, push, FIDO2, Windows Hello, passkeys — because the victim authenticates fully on their own device, and the attacker simply receives the resulting tokens.

The Device Code flow was designed for devices without browsers — smart TVs, IoT, CLI tools. The attacker generates a code, sends it to the victim under a convincing pretext, the victim authenticates on their own trusted device, and the attacker receives tokens.

```
ATTACKER                    AUTH SERVER                VICTIM
   │                            │                        │
   │  POST /devicecode          │                        │
   │  client_id + scope         │                        │
   │ ──────────────────────►    │                        │
   │                            │                        │
   │  user_code + URL           │                        │
   │ ◄──────────────────────    │                        │
   │                            │                        │
   │  Sends code to victim      │                        │
   │  via phishing pretext      │                        │
   │ ─────────────────────────────────────────────────►  │
   │                            │                        │
   │                            │  Victim authenticates  │
   │                            │  with FULL MFA         │
   │                            │ ◄───────────────────── │
   │                            │                        │
   │  Polls /token endpoint     │                        │
   │ ──────────────────────►    │                        │
   │                            │                        │
   │  access + refresh + id     │                        │
   │ ◄──────────────────────    │                        │
   │                            │                        │
   │  FULL ACCESS ACHIEVED      │                        │
```

**The critical insight:** The attacker never interacts with MFA. The victim completes the full flow — password, TOTP, push, FIDO2, biometrics, whatever — on their own trusted device. The attacker just receives the tokens.

### Multi-Cloud Device Code Targets

| Cloud Provider | What You Get | Persistence Value |
|---|---|---|
| **Azure / Entra ID** | access_token + refresh_token + id_token (JWT) | **HIGH** — refresh token = persistent access |
| **AWS SSO** | SSO access token → account list → role enum | MEDIUM — enumerate all accounts and roles |
| **GCP** | access_token + refresh_token for Google APIs | HIGH — persistent Workspace + GCP access |
| **GitHub** | access_token (repo, user, org scopes) | HIGH — source code, secrets, CI/CD |
| **GitLab** | access_token for GitLab API | HIGH — repo access, CI/CD variables |

**Post-capture operations** include token extraction, refresh token rotation for persistence, device registration in Azure AD, Azure DevOps enumeration, and PAT creation from stolen tokens.

**ATT&CK:** T1528 (Steal Application Access Token)

### 2.2 MFA Downgrade — Forcing FIDO2 Users to Weaker Methods

FIDO2 and Passkeys are phishing-resistant by design — the cryptographic challenge is domain-bound. An attacker's proxy domain fails verification. This is the one MFA type that AiTM cannot bypass.

So you make the login page forget FIDO2 exists.

MFA Downgrade is implemented as a Cloudflare Worker reverse-proxying `login.microsoftonline.com`. Four simultaneous techniques manipulate the auth flow:

```
VICTIM → CLOUDFLARE WORKER (MFA Downgrade) → login.microsoftonline.com

  Technique 1: Rewrite /GetCredentialType
               isFidoSupported → false
  
  Technique 2: UA Spoofing
               Non-FIDO User-Agent string
  
  Technique 3: JSON Config Manipulation
               FIDO isDefault → false
               Push isDefault → true
  
  Technique 4: CSS Injection
               Hide all FIDO/passkey DOM elements
               display: none !important
```

The server thinks the client doesn't support FIDO2. The user sees only TOTP/Push options. They complete weak MFA. The Worker captures `ESTSAUTH` and `ESTSAUTHPERSISTENT` cookies and exfiltrates them to a C2 webhook.

```javascript
// CSS payload — the final failsafe
const FIDO_HIDE_CSS = `
<style>
  div[data-value="FidoKey"],
  div[aria-label*="security key"],
  div[aria-label*="passkey"],
  button[aria-label*="security key"] {
    display: none !important;
    visibility: hidden !important;
    height: 0 !important;
  }
</style>`;
```

**ATT&CK:** T1556.006 (Modify Authentication Process — MFA)

### 2.3 Consent Grant Manipulation — Persistent API Access

Consent Grant attacks don't steal credentials or sessions — they grant the attacker's application persistent API access. The victim clicks "Accept" on an OAuth consent prompt, and the attacker's app can read their mail, files, and send messages on their behalf — indefinitely.

**Illicit Consent Grant** works by registering an Azure AD app with broad scopes, then tricking the user into granting consent. **ConsentFix** is a Cloudflare Worker variant that uses Azure CLI's own client ID (`04b07795-8ddb-461a-bbee-02f9e1bf7b46`) — a first-party Microsoft application already consented in every tenant. No admin consent required.

### 2.4 AiTM Session Hijacking

Evilginx operates as a reverse proxy — the victim interacts with the real login page through the attacker's server. The attacker captures the session cookie issued after successful authentication. Bypasses TOTP/SMS/Push. Cannot bypass FIDO2/Passkeys (which is why MFA Downgrade exists).

Serverless AiTM deployment via Terraform to Cloudflare Workers eliminates persistent infrastructure — no server to fingerprint or take down.

---

## Part 3: What You're Stealing — Token Taxonomy

| Token Type | Lifetime | Operator Value |
|---|---|---|
| **Access Token** | Minutes-hours | Immediate resource access. Use quickly. |
| **Refresh Token** | Days-months | **Highest value.** Persistent access without re-auth. Always capture. |
| **ID Token** | Short-lived | Recon — UPN, tenant ID, user object ID. Impersonation fuel. |
| **Session Cookie** | Hours-days | Full web app access. What AiTM captures. |
| **Primary Refresh Token** | Long-lived | **Extremely high value.** SSO across all Azure AD-integrated apps. |

JWTs (access/ID tokens from Entra ID) are Base64-encoded `header.payload.signature` — decode client-side to extract `aud` (audience), `sub` (subject), `tid` (tenant), `upn` (user), `scp` (scopes), `exp` (expiry).

---

## Part 4: MITRE ATT&CK Mapping

| Technique | ATT&CK ID | Tactic |
|---|---|---|
| Email Phishing (Link) | T1566.002 | Initial Access |
| AiTM / Evilginx | T1557.003 | Credential Access |
| Device Code Phishing | T1528 | Credential Access |
| Consent Grant | T1098.003 | Persistence |
| Session Cookie Theft | T1539 | Credential Access |
| Token Manipulation | T1134 | Privilege Escalation |
| ClickFix / FileFix | T1204.001 | Execution |
| HTA Execution | T1218.005 | Defense Evasion |
| MFA Downgrade | T1556.006 | Credential Access |

---

## Part 5: Detection Playbook — For the Blue Team

Every technique has detection surface.

**Device Code Phishing:** Filter Azure AD sign-in logs for `authenticationProtocol == deviceCode`. Block or require additional verification via Conditional Access. Alert on tokens used from unexpected geography.

**MFA Downgrade:** Monitor for users normally authenticating with FIDO2 suddenly falling back to TOTP/Push. Correlate User-Agent anomalies. Enforce authentication strength policies mandating FIDO2 for privileged accounts.

**Consent Grant:** Monitor Azure AD audit logs for `Consent to application` events with broad scopes. Deploy app governance policies. Restrict user consent to require admin approval.

**LOU Techniques:** Enable PowerShell ScriptBlock logging. Monitor clipboard write events from browser processes. Alert on `cmd.exe`/`powershell.exe` spawned from `explorer.exe`.

**Session Cookie Theft:** Deploy impossible travel detection. Enable continuous access evaluation (CAE). Require device compliance for session tokens.

---

## Real-World Precedent

**MGM Resorts (2023)** — Scattered Spider used vishing to social-engineer MGM's IT Help Desk into resetting MFA. Okta compromised → Entra ID breached → 6 TB exfiltrated → 100+ ESXi hypervisors encrypted. $100M impact. The entire chain started with a phone call.

**AI-Powered BEC (2025)** — Attackers combined AI voice cloning with AiTM phishing and MFA fatigue to compromise a CFO's mailbox. Live Zoom call with deepfake CEO audio authorized two wire transfers totaling $2.3M. First confirmed real-time AI voice BEC.

---

## Closing: The Door's Already Open

Here's the part where I'm supposed to tell you "stay safe out there" and sign off with a smiley face.

Nah.

The phishing landscape has shifted irreversibly. Credentials are a means to an end, but tokens, sessions, and consent grants are the prize. LOU techniques eliminate the need for malware entirely — the user's own actions become the attack vector.

For operators: master the decision matrix. Know when to use AiTM vs Device Code vs MFA Downgrade. Chain techniques together. Build modular infrastructure that survives burning.

For defenders: the detection opportunities exist at every stage. Device Code flow monitoring, consent grant governance, authentication method analysis, and continuous access evaluation are your highest-ROI controls.

If you're not monitoring these today — well...

*I just slid my d*** down your throat, and you thanked me for it.*

Yeah. Start monitoring.

---

**Next up:**  
→ *The OSINT Kill Chain* — digital investigations, credential leak discovery, and blockchain analysis  
→ *AI Architecture Attacks* — data poisoning, model manipulation, and LLM exploitation from the adversary's perspective

---

## References

1. **MITRE ATT&CK Framework** — [attack.mitre.org](https://attack.mitre.org/)
2. **Microsoft Security** — [OAuth 2.0 Device Code Flow documentation](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-device-code)
3. **Microsoft Security** — [Detecting Illicit Consent Grants](https://learn.microsoft.com/en-us/security/operations/incident-response-playbook-app-consent)
4. **mr.d0x** — Browser-in-the-Browser (BiTB) original research
5. **Proofpoint** — ClickFix social engineering technique analysis
6. **Mandiant** — Scattered Spider (UNC3944) threat intelligence and MGM incident reporting
7. **Evilginx** — [github.com/kgretzky/evilginx2](https://github.com/kgretzky/evilginx2)
8. **GoPhish** — [github.com/gophish/gophish](https://github.com/gophish/gophish)
9. **CyberWarFare Labs** — Offensive phishing operations research and tradecraft

---

*— 0xNegan*  
*Lucial is thirsty.*

[LinkedIn](https://www.linkedin.com/in/ahmed-13b6bb279) · [YouTube](https://youtube.com/@negansec) · [Telegram: Macroc](https://t.me/Macroc) · [Medium](https://medium.com/@CipherHawk/)
