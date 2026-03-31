# Security Playbook

> 22 sections, 100+ rules. Reference IDs in reviews: "Violates AUTH-02".
> Sources: OWASP Top 10:2025, ASVS 5.0, OWASP Agentic AI 2026, Appwrite Security Docs.

## 1. Foundational Principles

| Principle | Rule |
|-----------|------|
| Defense in Depth | Layer protections — never rely on a single control |
| Fail Closed | On error → deny access. Never fail open. |
| Least Privilege | Minimum permissions. Revoke when no longer needed. |
| Never Trust Input | Validate server-side. Client validation = UX only. |
| Secure by Default | Ship most restrictive. Users opt into less security. |

## 2. OWASP Top 10:2025

| # | Vulnerability | Prevention |
|---|---------------|------------|
| A01 | Broken Access Control | Deny by default, server-side, verify ownership |
| A02 | Security Misconfiguration | Harden configs, disable defaults |
| A03 | Supply Chain Failures | Lock versions, verify integrity, audit deps |
| A04 | Cryptographic Failures | TLS 1.2+, AES-256-GCM, Argon2/bcrypt |
| A05 | Injection | Parameterized queries, input validation |
| A06 | Insecure Design | Threat model, rate limit, design security upfront |
| A07 | Auth Failures | MFA, breached password checks, secure sessions |
| A08 | Integrity Failures | Sign packages, SRI for CDN |
| A09 | Logging Failures | Log security events, structured, alerting |
| A10 | Exception Handling | Fail-closed, hide internals, log with context |

## 3. Authentication & Sessions

**AUTH-01** — JWT max 7 days. Access tokens ≤15min. Refresh token rotation.
**AUTH-02** — Never AI-generated auth. Use Appwrite Auth / Clerk / Auth0.
**AUTH-03** — API keys in env vars only. Never hardcode.
**AUTH-04** — Argon2id/bcrypt/scrypt. Min 12 chars. Check breached lists.
**AUTH-05** — Session tokens 128+ bits entropy. Regenerate after login.
**AUTH-06** — MFA for account changes, payments, admin access.

**JWT**: Reject alg:none · Specify algorithm server-side · 256+ bit secrets · Always set exp · Store in httpOnly/Secure/SameSite=Strict cookies · Include jti for revocation.

**Cookies**: HttpOnly (no JS access) · Secure (HTTPS only) · SameSite=Strict (no CSRF) · Path=/ · Max-Age set.

## 4. Access Control

**AUTHZ-01** — Deny by default. Check auth on every request, not just routing.
**AUTHZ-02** — Verify ownership at data layer, not just route.
**AUTHZ-03** — UUIDs, not sequential IDs (prevent enumeration).
**AUTHZ-04** — Return 404 (not 403) for unauthorized resources.
**AUTHZ-05** — On user removal: revoke all tokens/sessions immediately.
**AUTHZ-06** — Whitelist mutable fields. Never accept full request body.

**Pitfalls**: IDOR (verify ownership) · Privilege escalation (validate roles server-side) · Horizontal access (check user_id = owner) · Mass assignment (whitelist fields) · Parent bypass (verify parent chain).

## 5. Input Handling & Injection

**INJ-01** — Parameterized queries always. Never concatenate user input into SQL.
**INJ-02** — Validate all input server-side. Length limits, type checks, allowlists.
**INJ-03** — Allowlist over denylist. Denylists always have gaps.

**SQL watch**: WHERE, ORDER BY (whitelist columns), LIMIT/OFFSET, table/column names (whitelist), IN clauses, LIKE patterns (escape wildcards).
**Command injection**: Use argument arrays, never shell=True or string interpolation.
**XXE**: Disable DTD and external entities in all XML parsers.
**Path traversal**: Canonicalize paths, validate against base directory, reject .. sequences.

## 6. Client-Side Security

**XSS Prevention**: Output encode per context (HTML/JS/URL/CSS) · CSP header (no unsafe-inline/unsafe-eval for scripts) · DOMPurify for user HTML · React JSX auto-escapes.

**CSP**: default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://api.yourdomain.com; frame-ancestors 'none'; base-uri 'self'; form-action 'self'

**CSRF**: Tokens on ALL state-changing endpoints · SameSite=Strict cookies · Missing token = reject (not skip) · No state changes on GET · Tokens not in URLs.

**Open Redirect**: Only accept relative paths (start with /, no //) or use indirect references (lookup table). Block: @ symbol, protocol tricks, double encoding, Unicode homographs, data URLs, null bytes.

## 7. Server-Side Security

**SSRF Prevention**: Allowlist domains · Block 169.254.169.254 (cloud metadata) · Resolve DNS before request, validate IP not private · Pin resolved IP · Limit redirect following.

**IP bypasses to block**: Decimal/octal/hex IPs · IPv6 localhost [::1] · IPv6 mapped IPv4 · Short notation · DNS rebinding · CNAME to internal.

**Errors**: No stack traces to users · Fail closed (deny on error) · Log with correlation ID · Generic client messages · Consistent error shape.

## 8. API Security

**API-01** — Auth + rate limiting on every endpoint. No exceptions.
**API-02** — CORS restricted to production domain. Never * on authed endpoints.
**API-03** — Validate redirect URLs against allowlist.
**API-04** — Remove console.log before production.

**Mass assignment**: Whitelist allowed update fields. Never User.update(req.body).
**GraphQL**: Disable introspection in prod · Depth limit (10) · Cost analysis · Batch limits.

## 9. Data Protection & Cryptography

**CRYPTO-01** — TLS 1.2+ everywhere. No HTTP fallback.
**CRYPTO-02** — AES-256-GCM for data at rest. Proper key management.
**CRYPTO-03** — No secrets in URLs, logs, or client code.
**CRYPTO-04** — Rotate secrets every 90 days.

**Never expose client-side**: API keys, DB strings, JWT secrets, encryption keys, OAuth secrets, internal URLs.
**Secret hiding spots**: JS bundles, source maps, HTML comments, hidden fields, data attrs, localStorage, SSR hydration data, NEXT_PUBLIC_* vars.

## 10. Database Security

**DB-01** — Row-Level Security from day one. Enable + FORCE on every table.
**DB-02** — Least privilege roles. Never superuser for app queries. Revoke public defaults.
**DB-03** — FK constraints always. Explicit ON DELETE (CASCADE/RESTRICT/SET NULL).
**DB-04** — DECIMAL for money. Never FLOAT/DOUBLE.
**DB-05** — Index all foreign keys.
**DB-06** — created_at/updated_at on every table.

**Anti-patterns**: VARCHAR(255) everywhere · FLOAT for money · Missing FKs · Unindexed FKs · Dates as strings · Non-reversible migrations.

## 11. File Upload Security

**UPLOAD-01** — Validate by magic bytes, not extension/MIME.
**UPLOAD-02** — Rename to random UUID. Discard original filename.
**UPLOAD-03** — Store outside webroot. CDN or separate domain.
**UPLOAD-04** — Per-user storage isolation.

**Magic bytes**: JPEG FF D8 FF · PNG 89 50 4E 47 · GIF 47 49 46 38 · PDF 25 50 44 46 · ZIP/DOCX 50 4B 03 04.
**Attacks**: Extension bypass · Null byte · Double extension · MIME spoofing · Polyglot files · SVG JS injection · XXE via DOCX · ZIP slip · ImageMagick exploit.
**Serve with**: Content-Disposition: attachment · X-Content-Type-Options: nosniff · Re-process images to strip metadata.

## 12. Infrastructure & Deployment

**INFRA-01** — DDoS protection (Cloudflare/Vercel edge). Non-negotiable.
**INFRA-02** — Cap AI API costs in code AND provider dashboard.
**INFRA-03** — Separate test/production. Different DBs, keys, domains.
**INFRA-04** — Test webhooks never touch production systems.
**INFRA-05** — Verify webhook signatures before processing.
**INFRA-06** — Automate backups. Test restores.
**INFRA-07** — HTTPS only. HSTS enabled.

## 13. Dependency & Supply Chain

**DEPS-01** — Verify packages before installing (downloads, maintainer, vulns).
**DEPS-02** — Latest secure versions. Pin in lockfiles.
**DEPS-03** — npm audit fix after every build. Critical vulns block deploy.
**DEPS-04** — SRI hashes for CDN resources.
**DEPS-05** — Lock versions, verify integrity hashes.

**Attack vectors**: Typosquatting · Dependency confusion · Malicious maintainer · Build script injection (review postinstall).

## 14. Logging & Monitoring

**LOG-01** — Log: deletions, role changes, payments, exports, login attempts, permission changes.
**LOG-02** — Real account deletion flows. GDPR/CCPA compliant.
**LOG-03** — Server-side permission enforcement. UI checks are cosmetic.

**Log fields**: timestamp, user_id, IP, event, correlation_id, resource details.
**Never log**: passwords, full card numbers, SSNs, session tokens, API keys, JWT secrets.

## 15. Agentic AI Security (OWASP 2026)

| Risk | Mitigation |
|------|------------|
| ASI01 Goal Hijack | Input sanitization, goal boundaries, behavioral monitoring |
| ASI02 Tool Misuse | Least privilege, validate all I/O |
| ASI03 Privilege Abuse | Short-lived scoped tokens |
| ASI04 Supply Chain | Verify signatures, sandbox plugins |
| ASI05 Code Execution | Sandbox, static analysis, human gates |
| ASI06 Memory Poisoning | Validate stored content, segment by trust |
| ASI07 Agent Comms | Authenticate, encrypt, verify integrity |
| ASI08 Cascading Failures | Circuit breakers, graceful degradation |
| ASI09 Trust Exploitation | Label AI content, verification steps |
| ASI10 Rogue Agents | Behavior monitoring, kill switches |

## 16. PII & Sensitive Data

**PII-01** — Detect and redact PII before logging.
**PII-02** — Mask in UI: last 4 digits for cards, ***-**-1234 for SSN.
**PII-03** — Minimize collection. TLS only. Process locally where possible.
**PII-04** — Field-level encryption on sensitive DB columns.
**PII-05** — Retention policies. Right-to-deletion (GDPR Art. 17).

**Detection**: Credit cards (Luhn 13-19 digits) · SSN (NNN-NN-NNNN) · API keys (sk-*, ghp_*, AKIA*) · Emails · Phone numbers · IPs.

## 17. Payment Security

**PAY-01** — Verify webhook signatures with provider SDK.
**PAY-02** — Use PCI-compliant providers. Never handle raw card data.
**PAY-03** — Server-side amount validation. Never trust client totals.
**PAY-04** — Idempotency keys on all payment operations.
**PAY-05** — Latest stable API version from provider.

## 18. Security Headers

Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
Content-Security-Policy: [see §6 CSP above]
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Cache-Control: no-store
Permissions-Policy: camera=(), microphone=(), geolocation=()

## 20. JS/TS Security Quirks

**Prototype pollution**: Never Object.assign(target, userInput) — use Object.create(null) or validate keys.
**Dangerous APIs to avoid with user input**: dynamic code evaluation, innerHTML, document.write(), __proto__ manipulation.

## 21. ASVS 5.0 Tiers

**L1 (All)**: 12+ char passwords, breached list check, rate limiting, 128+ bit tokens, HTTPS, input validation.
**L2 (Sensitive)**: + MFA, key management, security logging, headers, dep auditing.
**L3 (Critical)**: + HSM, threat modeling, pen testing, incident response plan.

## 22. Master Checklists

### Pre-Commit
No secrets · No console.log · Inputs validated server-side · Queries parameterized · Auth on endpoints · Rate limiting · Generic errors · No dynamic code evaluation with user input · Upload magic bytes · Mass assignment blocked

### Pre-Deploy
npm audit clean · Security headers (HSTS/CSP) · HTTPS only · Env vars for secrets · RLS enabled · Backups verified · Env separated · Webhooks verified · DDoS protection · AI cost caps

### Code Review Security Pass
Access control (deny-default, ownership at data layer) · Input (validated, sanitized, parameterized) · Output encoding (context-appropriate) · Auth on every endpoint · CSRF tokens · No secrets in code/URLs/logs/bundles · Errors fail-closed · Deps audited · Uploads validated · DB: RLS + least-privilege

### Incident Response
Rotate compromised secrets · Revoke sessions/tokens · Assess exposure scope · Notify users per regulation · Document root cause · Update tasks/gotchas.md · Implement prevention · Pen test verification
