# Security Playbook

> Comprehensive security reference for AI-assisted development.
> Sources: OWASP Top 10:2025, ASVS 5.0, OWASP Agentic AI 2026, Appwrite Security Docs, and battle-tested vibe coding rules.
>
> **How to use this document**: Apply these rules to every feature, pull request, and deployment. Reference specific rule numbers in code reviews (e.g., "Violates AUTH-02"). Use the checklists at the end of each section for verification.

---

## Table of Contents

1. [Foundational Principles](#1-foundational-principles)
2. [OWASP Top 10:2025 Quick Reference](#2-owasp-top-102025-quick-reference)
3. [Authentication & Sessions](#3-authentication--sessions)
4. [Access Control](#4-access-control)
5. [Input Handling & Injection Prevention](#5-input-handling--injection-prevention)
6. [Client-Side Security](#6-client-side-security)
7. [Server-Side Security](#7-server-side-security)
8. [API Security](#8-api-security)
9. [Data Protection & Cryptography](#9-data-protection--cryptography)
10. [Database Security](#10-database-security)
11. [File Upload Security](#11-file-upload-security)
12. [Infrastructure & Deployment](#12-infrastructure--deployment)
13. [Dependency & Supply Chain Security](#13-dependency--supply-chain-security)
14. [Logging, Monitoring & Error Handling](#14-logging-monitoring--error-handling)
15. [Agentic AI Security](#15-agentic-ai-security)
16. [PII & Sensitive Data Handling](#16-pii--sensitive-data-handling)
17. [Payment & Financial Security](#17-payment--financial-security)
18. [Security Headers Reference](#18-security-headers-reference)
19. [Appwrite Security Guide](#19-appwrite-security-guide)
20. [Language-Specific Security Quirks](#20-language-specific-security-quirks)
21. [ASVS 5.0 Compliance Tiers](#21-asvs-50-compliance-tiers)
22. [Master Checklists](#22-master-checklists)

---

## 1. Foundational Principles

These five principles underpin every rule in this playbook. When in doubt, fall back to these.

| Principle | Description |
|-----------|-------------|
| **Defense in Depth** | Never rely on a single security control. Layer protections so that if one fails, others still hold. |
| **Fail Closed** | When something breaks, deny access. Never fail open. |
| **Least Privilege** | Grant the minimum permissions necessary. Revoke immediately when no longer needed. |
| **Never Trust Input** | Validate everything server-side. Client-side validation is UX, not security. |
| **Secure by Default** | Ship with the most restrictive settings. Let users opt into less security, never the reverse. |

---

## 2. OWASP Top 10:2025 Quick Reference

| # | Vulnerability | Key Prevention |
|---|---------------|----------------|
| A01 | Broken Access Control | Deny by default, enforce server-side, verify ownership |
| A02 | Security Misconfiguration | Harden configs, disable defaults, minimize features |
| A03 | Supply Chain Failures | Lock versions, verify integrity, audit dependencies |
| A04 | Cryptographic Failures | TLS 1.2+, AES-256-GCM, Argon2/bcrypt for passwords |
| A05 | Injection | Parameterized queries, input validation, safe APIs |
| A06 | Insecure Design | Threat model, rate limit, design security controls upfront |
| A07 | Auth Failures | MFA, check breached passwords, secure session handling |
| A08 | Integrity Failures | Sign packages, SRI for CDN, safe serialization |
| A09 | Logging Failures | Log security events, structured format, alerting |
| A10 | Exception Handling | Fail-closed, hide internals, log with context |

---

## 3. Authentication & Sessions

### Rules

**AUTH-01 — Session Lifetime.**
Set session expiration limits. JWT sessions should never exceed 7 days and must use refresh token rotation. Access tokens should be short-lived (15 minutes or less).

**AUTH-02 — Never use AI-generated auth.**
Use battle-tested providers: Clerk, Supabase Auth, or Auth0. Rolling your own authentication — or letting an AI build it — is a critical risk.

**AUTH-03 — Secure API keys.**
Due to chat access in AI-assisted workflows, keep API keys strictly secured. Use `process.env` keys. Never hardcode secrets in source code.

**AUTH-04 — Password storage.**
Hash passwords with Argon2id, bcrypt, or scrypt. Never use MD5, SHA1, or plain SHA256. Minimum 12 characters, check against breached password lists.

**AUTH-05 — Session token entropy.**
Session tokens must have 128+ bits of entropy. Regenerate tokens after login to prevent session fixation.

**AUTH-06 — MFA for sensitive operations.**
Multi-factor authentication should be available for account changes, payment actions, and admin access.

### JWT Security

| Vulnerability | Prevention |
|---------------|------------|
| `alg: none` attack | Always verify algorithm server-side, reject `none` |
| Algorithm confusion | Explicitly specify expected algorithm, never derive from token |
| Weak HMAC secrets | Use 256+ bit cryptographically random secrets |
| Missing expiration | Always set `exp` claim |
| Token in localStorage | Store in httpOnly, Secure, SameSite=Strict cookies |

**Secure JWT Implementation:**

```javascript
// SIGNING — always use environment variables
const token = jwt.sign(
  {
    sub: userId,
    iat: Math.floor(Date.now() / 1000),
    exp: Math.floor(Date.now() / 1000) + 15 * 60, // 15 mins
    jti: crypto.randomUUID(), // unique ID for revocation
  },
  process.env.JWT_SECRET,
  { algorithm: "HS256" }
);

// SENDING — protect against XSS and CSRF
res.cookie("token", token, {
  httpOnly: true,
  secure: true,
  sameSite: "strict",
});

// VERIFYING — whitelist the algorithm
jwt.verify(token, secret, { algorithms: ["HS256"] });
```

### Cookie Configuration

```
Set-Cookie: session=<token>; SameSite=Strict; Secure; HttpOnly; Path=/; Max-Age=900
```

| Flag | Purpose |
|------|---------|
| `HttpOnly` | Prevents JavaScript access (mitigates XSS token theft) |
| `Secure` | Only sent over HTTPS |
| `SameSite=Strict` | Prevents cross-site request attachment (mitigates CSRF) |
| `Path=/` | Scoped to entire application |
| `Max-Age` | Enforces expiration at browser level |

### Session Checklist

- [ ] Sessions invalidated on logout
- [ ] Tokens regenerated after login
- [ ] Refresh token rotation implemented (old token invalidated on use)
- [ ] `exp` claim always set and validated
- [ ] Tokens stored in httpOnly cookies (not localStorage/sessionStorage)
- [ ] Algorithm explicitly specified on verification
- [ ] `alg: none` rejected
- [ ] Secret is 256+ bits of random data
- [ ] MFA available for sensitive operations
- [ ] Rate limiting on authentication endpoints

---

## 4. Access Control

### Rules

**AUTHZ-01 — Deny by default.**
Every request is unauthorized until explicitly permitted. Check authorization on every request, not just at routing.

**AUTHZ-02 — Verify ownership at the data layer.**
Each user must only access/modify their own data. Verify ownership in the database query, not just the route.

**AUTHZ-03 — Use UUIDs, not sequential IDs.**
Use UUIDv4 or similar non-guessable identifiers for all resources to prevent enumeration. Only use sequential IDs if explicitly required by the project.

**AUTHZ-04 — Return 404 for unauthorized resources.**
Return `404 Not Found` instead of `403 Forbidden` when a user lacks access. This prevents resource enumeration.

**AUTHZ-05 — Account lifecycle.**
When a user is removed from an organization: immediately revoke all access tokens and sessions. When an account is deleted/deactivated: invalidate all active sessions and API keys.

**AUTHZ-06 — Filter mutable fields.**
Never blindly accept all request body fields. Whitelist which fields a request can update to prevent mass assignment privilege escalation.

### Implementation Pattern

```python
def get_resource(resource_id, current_user):
    resource = database.find(resource_id)

    if resource is None:
        return 404  # Don't reveal if resource exists

    if resource.owner_id != current_user.id:
        if not current_user.has_org_access(resource.org_id):
            return 404  # Return 404, not 403, to prevent enumeration

    return resource
```

### Common Pitfalls

| Pitfall | Description | Prevention |
|---------|-------------|------------|
| IDOR | Accessing another user's resource by changing the ID | Verify ownership on every request |
| Privilege Escalation | Regular user accessing admin functionality | Validate roles server-side |
| Horizontal Access | User A accessing User B's data at the same level | Check user_id matches resource owner |
| Mass Assignment | Accepting `{ role: "admin" }` in request body | Whitelist allowed update fields |
| Missing Re-validation | Not re-checking permissions after privilege change | Re-validate on every request |
| Parent Resource Bypass | Accessing a child without checking parent ownership | Verify parent chain (comment → post → author) |

### Access Control Checklist

- [ ] Authorization checked on every request (server-side)
- [ ] Framework-level auth middleware in place
- [ ] Ownership verified at the data layer
- [ ] Deny-by-default policy enforced
- [ ] Privilege escalation paths reviewed
- [ ] Role changes validated server-side, never from client
- [ ] Parent resource ownership checked
- [ ] Request body fields whitelisted (no mass assignment)
- [ ] UUIDs used for resource identifiers

---

## 5. Input Handling & Injection Prevention

### Rules

**INJ-01 — Parameterized queries always.**
Never concatenate user input into SQL queries. Use prepared statements or ORM methods that parameterize automatically.

**INJ-02 — Validate all input server-side.**
Client-side validation is for UX. Server-side validation is for security. Enforce input length limits, type checks, and allowlist validation.

**INJ-03 — Allowlist over denylist.**
Validate against what IS allowed, not what ISN'T. Denylists always have gaps.

### SQL Injection

```python
# UNSAFE — string concatenation
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")

# SAFE — parameterized query
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

**Injection points to watch:**

- WHERE clauses
- ORDER BY clauses (cannot parameterize — must whitelist column names)
- LIMIT / OFFSET values
- Table and column names (cannot parameterize — must whitelist)
- IN clauses with dynamic lists
- LIKE patterns (escape `%` and `_` wildcards)

### Command Injection

```python
# UNSAFE — shell injection
os.system(f"convert {filename} output.png")

# SAFE — no shell, argument list
subprocess.run(["convert", filename, "output.png"], shell=False)
```

### XML External Entity (XXE)

Any XML parser that processes user-supplied XML is vulnerable. This includes SOAP APIs, XML-RPC, XML file uploads, Office documents (DOCX/XLSX/PPTX are ZIP with XML), SVG files, SAML assertions, and RSS/Atom feeds.

**Prevention by language:**

```java
// Java — disable DTD and external entities
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
dbf.setFeature("http://xml.org/sax/features/external-general-entities", false);
dbf.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
dbf.setExpandEntityReferences(false);
```

```python
# Python — use defusedxml or disable resolution
from lxml import etree
parser = etree.XMLParser(resolve_entities=False, no_network=True)
```

```javascript
// Node.js — use libraries that disable DTD by default
// If using libxmljs: { noent: false, dtdload: false }
```

```csharp
// .NET — prohibit DTD processing
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
settings.XmlResolver = null;
```

### Path Traversal

```python
# UNSAFE — user controls the path
file_path = "/uploads/" + user_input

# SAFE — canonicalize and validate
import os

def safe_join(base_directory, user_path):
    base = os.path.abspath(os.path.realpath(base_directory))
    target = os.path.abspath(os.path.realpath(os.path.join(base, user_path)))
    if os.path.commonpath([base, target]) != base:
        raise ValueError("Path traversal detected")
    return target
```

**Additional input sanitization:** Remove or reject `..` sequences, reject absolute path indicators (`/`, `C:`), whitelist allowed characters (alphanumeric, dash, underscore), validate file extension if applicable.

### Input Handling Checklist

- [ ] All user input validated server-side
- [ ] Using parameterized queries (not string concatenation)
- [ ] Input length limits enforced
- [ ] Allowlist validation preferred over denylist
- [ ] ORDER BY, table/column names use whitelists
- [ ] XML parsers have DTD and external entities disabled
- [ ] File paths canonicalized and validated against base directory
- [ ] No `eval()`, `exec()`, or equivalent with user input

---

## 6. Client-Side Security

### Cross-Site Scripting (XSS)

Every input controllable by the user must be sanitized against XSS.

**Direct input sources:** Form fields (email, name, bio, comments), search queries, file names during upload, rich text editor content.

**Indirect input sources:** URL parameters and query strings, URL fragments, HTTP headers used in the app (Referer, User-Agent if displayed), third-party API data displayed to users, WebSocket messages, postMessage data from iframes, localStorage/sessionStorage values if rendered.

**Often overlooked vectors:** Error messages that reflect user input, PDF/document generators that accept HTML, email templates with user data, log viewers in admin panels, JSON responses rendered as HTML, SVG file uploads (can contain JavaScript), Markdown rendering if allowing HTML.

**Protection strategies:**

1. **Output encoding** (context-specific) — HTML encode for HTML context, JavaScript escape for JS context, URL encode for URL context, CSS escape for CSS context. Use framework built-in escaping (React JSX, Vue `{{ }}`).

2. **Content Security Policy** (CSP):
```
Content-Security-Policy:
  default-src 'self';
  script-src 'self';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  font-src 'self';
  connect-src 'self' https://api.yourdomain.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';
```
Avoid `'unsafe-inline'` and `'unsafe-eval'` for scripts. Use nonces or hashes when inline scripts are necessary. Report violations with `report-uri /csp-report`.

3. **Input sanitization** — Use DOMPurify for user-generated HTML. Whitelist allowed tags/attributes for rich text. Strip dangerous patterns.

### Cross-Site Request Forgery (CSRF)

Every state-changing endpoint must be protected against CSRF.

**Endpoints requiring CSRF protection:** All POST, PUT, PATCH, DELETE requests; file uploads; settings changes; payment endpoints; login/signup/password endpoints; OAuth callbacks.

**Protection mechanisms:**

1. **CSRF tokens** — cryptographically random, tied to session, validated on every mutation, regenerated after login
2. **SameSite cookies** — `Set-Cookie: session=abc; SameSite=Strict; Secure; HttpOnly`
3. **Double submit cookie** — token in both cookie and request body/header, server validates match

**Critical edge cases:**

- CSRF validation must NOT depend on whether the token is present — always require it
- JSON APIs are not immune — validate Origin/Referer headers AND use tokens
- CORS misconfiguration can bypass SameSite cookies
- GET requests must never cause side effects
- CSRF tokens must not appear in URLs (leaked via Referer header)
- Prefer custom headers (`X-CSRF-Token`) over URL parameters

### CSRF Checklist

- [ ] Token is cryptographically random
- [ ] Token is tied to user session
- [ ] Token validated server-side on all state-changing requests
- [ ] Missing token = rejected request (not skipped validation)
- [ ] Token regenerated on authentication state change
- [ ] SameSite cookie attribute set
- [ ] Secure and HttpOnly flags on session cookies
- [ ] No state changes on GET requests

### Open Redirect

Any endpoint accepting a URL for redirection must validate against an allowlist or use relative paths only.

**Bypass techniques to block:**

| Technique | Example | Risk |
|-----------|---------|------|
| @ symbol | `https://legit.com@evil.com` | Browser navigates to evil.com |
| Subdomain abuse | `https://legit.com.evil.com` | evil.com owns the subdomain |
| Protocol tricks | `javascript:alert(1)` | XSS via redirect |
| Double URL encoding | `%252f%252fevil.com` | Decodes to `//evil.com` |
| Unicode homograph | `https://legіt.com` (Cyrillic і) | IDN homograph attack |
| Protocol-relative | `//evil.com` | Uses current page's protocol |
| Data URLs | `data:text/html,<script>...` | Direct payload execution |
| Backslash | `https://legit.com\@evil.com` | Some parsers normalize `\` to `/` |
| Null byte | `https://legit.com%00.evil.com` | Some parsers truncate at null |

**Best approach:** Only accept relative paths (must start with `/`, must not contain `//`), or use indirect references (`?redirect=dashboard` → lookup table).

**IDN Homograph protection:** Convert URLs to Punycode before validation. Consider blocking non-ASCII domains for sensitive redirects.

### Client-Side Checklist

- [ ] All output encoded for context (HTML, JS, URL, CSS)
- [ ] CSP header deployed with no `unsafe-inline` for scripts
- [ ] DOMPurify (or equivalent) used for any user-generated HTML
- [ ] CSRF tokens on all state-changing endpoints
- [ ] SameSite=Strict on session cookies
- [ ] No state changes on GET requests
- [ ] Redirect URLs validated against allowlist
- [ ] `X-Content-Type-Options: nosniff` set
- [ ] CSP violation reporting configured

---

## 7. Server-Side Security

### Server-Side Request Forgery (SSRF)

Any feature where the server makes requests to user-provided URLs is vulnerable.

**Vulnerable features:** Webhooks (user provides callback URL), URL previews, PDF generators from URLs, image/file fetching from URLs, import-from-URL features, RSS/feed readers, API integrations with user-provided endpoints, proxy functionality, HTML-to-PDF/image converters.

**Protection strategies:**

1. **Allowlist approach** (preferred) — only allow requests to pre-approved domains
2. **Network segmentation** — run URL-fetching services in isolated network
3. **DNS validation** — resolve DNS before the request, validate IP is not private/internal, pin the IP
4. **Block cloud metadata** — `169.254.169.254` (AWS, GCP, Azure, DigitalOcean), `metadata.google.internal`
5. **Limit redirects** — if following, validate each hop

**IP and DNS bypass techniques to block:**

| Technique | Example | Description |
|-----------|---------|-------------|
| Decimal IP | `http://2130706433` | 127.0.0.1 as decimal |
| Octal IP | `http://0177.0.0.1` | Octal representation |
| Hex IP | `http://0x7f.0x0.0x0.0x1` | Hexadecimal |
| IPv6 localhost | `http://[::1]` | IPv6 loopback |
| IPv6 mapped IPv4 | `http://[::ffff:127.0.0.1]` | IPv4-mapped IPv6 |
| Short IPv6 | `http://[::]` | All zeros |
| Short IPv4 | `http://127.1` | Shortened notation |
| DNS rebinding | Attacker DNS returns internal IP | First resolution external, second internal |
| CNAME to internal | Attacker domain CNAMEs to internal | DNS points to internal hostname |
| Redirect chains | External → 302 → internal | Follow redirects carefully |

**DNS rebinding prevention:**
1. Resolve DNS before making the request
2. Validate the resolved IP is not internal/private
3. Pin the resolved IP for the request (don't re-resolve)
4. Or: resolve twice with a delay, ensure both resolve to same external IP

### Error Handling

```python
# UNSAFE — exposes internals
@app.errorhandler(Exception)
def handle_error(e):
    return str(e), 500

# SAFE — fail closed, log context
@app.errorhandler(Exception)
def handle_error(e):
    error_id = uuid.uuid4()
    logger.exception(f"Error {error_id}: {e}")
    return {"error": "An error occurred", "id": str(error_id)}, 500
```

**Error handling rules:**

- No stack traces exposed to users in production
- Fail-closed on errors (deny, not allow)
- All exceptions logged with context and correlation ID
- Consistent error response shape (no information leakage via different error formats)
- Generic messages to client, detailed logs server-side

### Fail-Closed Pattern

```python
# UNSAFE — fail open (DANGEROUS)
def check_permission(user, resource):
    try:
        return auth_service.check(user, resource)
    except Exception:
        return True  # Grants access on error!

# SAFE — fail closed
def check_permission(user, resource):
    try:
        return auth_service.check(user, resource)
    except Exception as e:
        logger.error(f"Auth check failed: {e}")
        return False  # Deny on error
```

### Server-Side Checklist

- [ ] SSRF: All user-provided URLs validated against allowlist
- [ ] SSRF: Cloud metadata IPs blocked (`169.254.169.254`)
- [ ] SSRF: DNS resolved and validated before request
- [ ] SSRF: Redirect following limited and each hop validated
- [ ] Errors return generic messages, never stack traces
- [ ] All error paths fail closed (deny access)
- [ ] Exceptions logged with correlation IDs
- [ ] Request timeouts configured on all outbound calls
- [ ] Response size limits set on outbound calls

---

## 8. API Security

### Rules

**API-01 — Auth and rate limiting on every endpoint.**
No exceptions. Every endpoint gets authentication and rate limiting.

**API-02 — CORS restricted to production domain.**
Use CORS to restrict access to your allow-listed production domain only. Never use `Access-Control-Allow-Origin: *` on authenticated endpoints.

**API-03 — Validate redirect URLs against an allowlist.**
All redirect URLs must be checked against an explicit allowlist.

**API-04 — Remove console.log before production.**
Remove all `console.log` statements before deploying to production. They can leak sensitive data in server logs.

### Mass Assignment Prevention

```javascript
// VULNERABLE — user can set { role: "admin" }
User.update(req.body);

// SECURE — whitelist allowed fields
const allowed = ["name", "email", "avatar"];
const updates = pick(req.body, allowed);
User.update(updates);
```

This applies to any ORM/framework — always explicitly define which fields a request can modify.

### GraphQL Security

| Vulnerability | Prevention |
|---------------|------------|
| Introspection in production | Disable introspection in prod environments |
| Query depth attack | Implement depth limiting (max 10 levels) |
| Query complexity attack | Enforce strict query cost limits |
| Batching attack | Limit operations per single request |

```javascript
const server = new ApolloServer({
  introspection: process.env.NODE_ENV !== "production",
  validationRules: [depthLimit(10), costAnalysis({ maximumCost: 1000 })],
});
```

### API Checklist

- [ ] Auth on every endpoint
- [ ] Rate limiting on every endpoint
- [ ] CORS restricted to allow-listed domains
- [ ] No `console.log` in production
- [ ] Request bodies filtered — only whitelisted fields accepted
- [ ] GraphQL introspection disabled in production
- [ ] Query depth and complexity limits enforced
- [ ] All redirect URLs validated against allowlist
- [ ] API versioning strategy in place

---

## 9. Data Protection & Cryptography

### Rules

**CRYPTO-01 — TLS everywhere.**
All data in transit encrypted with TLS 1.2+. No exceptions. No HTTP fallback.

**CRYPTO-02 — Encrypt sensitive data at rest.**
Use AES-256-GCM for symmetric encryption. Manage keys through a proper key management service.

**CRYPTO-03 — No sensitive data in URLs or logs.**
Secrets, tokens, passwords, and PII must never appear in URL parameters, query strings, or log output.

**CRYPTO-04 — Rotate secrets every 90 days.**
Set a rotation schedule and enforce it. Applies to API keys, database passwords, JWT secrets, and encryption keys.

### Secret Exposure Prevention

**Never expose in client-side code:**

- Third-party API keys (Stripe, AWS, etc.)
- Database connection strings
- JWT signing secrets
- Encryption keys
- OAuth client secrets
- Internal service URLs/credentials

**Where secrets hide (check these):**

- JavaScript bundles (including source maps)
- HTML comments
- Hidden form fields
- Data attributes
- localStorage / sessionStorage
- Initial state / hydration data in SSR apps
- Build-tool prefixed env vars (`NEXT_PUBLIC_*`, `REACT_APP_*`)

**Infrastructure details to protect:**

- Internal IP addresses
- Database schemas
- Debug information and stack traces
- Server software versions

### Data Protection Checklist

- [ ] TLS 1.2+ on all connections
- [ ] Sensitive data encrypted at rest (AES-256-GCM)
- [ ] No secrets in URLs, logs, or client-side code
- [ ] Secrets rotation schedule in place (90 days)
- [ ] Key management through proper service
- [ ] Source maps not deployed to production
- [ ] No PII in error messages or logs
- [ ] `NEXT_PUBLIC_*` / `REACT_APP_*` vars reviewed for secrets

---

## 10. Database Security

### Rules

**DB-01 — Row-Level Security from day one.**
Enable RLS on every table. Enforce data access at the database level so that even a buggy application layer cannot leak data.

```sql
-- Enable RLS
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Create policy: users only see their own orders
CREATE POLICY orders_user_policy ON orders
  FOR ALL
  TO authenticated
  USING (user_id = auth.uid());

-- Force RLS even for table owners
ALTER TABLE orders FORCE ROW LEVEL SECURITY;
```

**DB-02 — Least privilege for database roles.**
Never use superuser for application queries. Create separate roles with specific, minimal grants.

```sql
-- Create read-only role
CREATE ROLE app_readonly NOLOGIN;
GRANT USAGE ON SCHEMA public TO app_readonly;
GRANT SELECT ON public.products, public.categories TO app_readonly;

-- Create write role with limited scope (no DELETE)
CREATE ROLE app_writer NOLOGIN;
GRANT USAGE ON SCHEMA public TO app_writer;
GRANT SELECT, INSERT, UPDATE ON public.orders TO app_writer;
GRANT USAGE ON SEQUENCE orders_id_seq TO app_writer;

-- Revoke dangerous defaults
REVOKE ALL ON SCHEMA public FROM public;
REVOKE ALL ON ALL TABLES IN SCHEMA public FROM public;
```

**DB-03 — Foreign key constraints always.**
Every relationship must have a foreign key constraint with an explicit ON DELETE strategy.

| Strategy | Use When |
|----------|----------|
| CASCADE | Dependent data (order_items when order deleted) |
| RESTRICT | Critical references (prevent accidental deletion) |
| SET NULL | Optional relationships |

**DB-04 — Use DECIMAL for money.**
Never use FLOAT/DOUBLE for financial values. Use `DECIMAL(10,2)` or store amounts in smallest unit (cents) as INTEGER.

**DB-05 — Index all foreign keys.**
Missing FK indexes cause slow JOINs. Always index foreign key columns.

**DB-06 — Timestamps on everything.**
Every table should have `created_at` and `updated_at` columns for audit trails.

### Schema Security Anti-Patterns

| Avoid | Why | Instead |
|-------|-----|---------|
| `VARCHAR(255)` everywhere | Wastes storage, hides intent | Size appropriately per field |
| `FLOAT` for money | Rounding errors | `DECIMAL(10,2)` |
| Missing FK constraints | Orphaned data | Always define foreign keys |
| No indexes on FKs | Slow JOINs | Index every foreign key |
| Storing dates as strings | Can't compare/sort properly | Use DATE/TIMESTAMP types |
| Non-reversible migrations | Can't rollback | Always write DOWN migration |

### Database Security Checklist

- [ ] RLS enabled and policies defined on every table
- [ ] `FORCE ROW LEVEL SECURITY` applied
- [ ] Application uses least-privilege database role
- [ ] Default `public` schema permissions revoked
- [ ] Foreign key constraints on all relationships
- [ ] ON DELETE strategy defined for each FK
- [ ] All foreign keys indexed
- [ ] Sensitive columns encrypted at rest
- [ ] No raw SQL with user input (parameterized only)
- [ ] Database credentials rotated on schedule
- [ ] Connection pooling configured
- [ ] `created_at` / `updated_at` on every table

---

## 11. File Upload Security

### Rules

**UPLOAD-01 — Validate by signature, not extension.**
Check magic bytes / file signatures. Never rely solely on file extension or MIME type.

**UPLOAD-02 — Rename all uploads.**
Use random UUID filenames. Discard the original filename entirely.

**UPLOAD-03 — Store outside webroot.**
Uploaded files must not be directly accessible or executable. Use a CDN or separate domain.

**UPLOAD-04 — Lock down storage per-user.**
Users can only access their own uploaded files. Enforce at the storage layer.

### Common File Upload Attacks

| Attack | Description | Prevention |
|--------|-------------|------------|
| Extension bypass | `shell.php.jpg` | Check full extension, use allowlist |
| Null byte | `shell.php%00.jpg` | Sanitize filename, check for null bytes |
| Double extension | `shell.jpg.php` | Only allow single extension |
| MIME type spoofing | Content-Type set to image/jpeg | Validate magic bytes |
| Magic byte injection | Valid magic bytes prepended to malicious file | Check entire file structure |
| Polyglot files | File valid as both JPEG and JavaScript | Parse as expected type, reject if invalid |
| SVG with JavaScript | `<svg onload="alert(1)">` | Sanitize SVG or disallow |
| XXE via upload | Malicious DOCX/XLSX (XML inside) | Disable external entities in parser |
| ZIP slip | `../../../etc/passwd` in archive | Validate all extracted paths |
| ImageMagick exploit | Specially crafted images | Keep updated, use policy.xml |
| Filename injection | `; rm -rf /` in filename | Sanitize filenames, use random names |
| Content-type confusion | Browser MIME sniffing | `X-Content-Type-Options: nosniff` |

### Magic Bytes Reference

| Type | Magic Bytes (hex) |
|------|-------------------|
| JPEG | `FF D8 FF` |
| PNG | `89 50 4E 47 0D 0A 1A 0A` |
| GIF | `47 49 46 38` |
| PDF | `25 50 44 46` |
| ZIP / DOCX / XLSX | `50 4B 03 04` |

### Secure Upload Handling

1. Validate magic bytes against expected type
2. Rename to random UUID
3. Store outside webroot or on separate domain
4. Serve with `Content-Disposition: attachment`, `X-Content-Type-Options: nosniff`, correct `Content-Type`
5. Set restrictive permissions — uploaded files must not be executable
6. For images: re-process through an image library to strip metadata and detect malformed files

### File Upload Checklist

- [ ] File type validated by magic bytes (not just extension)
- [ ] File size limits enforced server-side (and per-type)
- [ ] Files renamed to random UUIDs
- [ ] Files stored outside webroot
- [ ] User storage isolation enforced
- [ ] SVG files sanitized or disallowed
- [ ] ZIP extraction validates all paths
- [ ] `X-Content-Type-Options: nosniff` on all served uploads
- [ ] Images re-processed to strip metadata

---

## 12. Infrastructure & Deployment

### Rules

**INFRA-01 — DDoS protection.**
Add DDoS protection via Cloudflare, Vercel edge config, or equivalent. Non-negotiable for production.

**INFRA-02 — Cap AI API costs.**
Set hard spending limits in both your code and the provider dashboard. Runaway API calls can bankrupt a project overnight.

**INFRA-03 — Separate test and production.**
Keep test and production environments fully separate. Different databases, different API keys, different domains.

**INFRA-04 — Webhooks must not touch real systems in test.**
Never let test webhooks hit production services. Use separate endpoints and test keys.

**INFRA-05 — Verify webhook signatures.**
Always verify webhook signatures before processing payment or sensitive data. Use the provider's official SDK.

**INFRA-06 — Automate and test backups.**
Automate backups, then actually test restoring from them. An untested backup is useless.

**INFRA-07 — HTTPS only.**
Enforce HTTPS with HSTS. No HTTP fallback.

### Infrastructure Checklist

- [ ] DDoS protection configured
- [ ] AI API cost caps set in code and provider dashboard
- [ ] Test and production fully separated
- [ ] Test webhooks isolated from production systems
- [ ] Webhook signatures verified before processing
- [ ] Backups automated AND restoration tested
- [ ] HTTPS enforced with HSTS
- [ ] Environment variables used for all configuration
- [ ] Server software versions hidden from responses
- [ ] Unnecessary ports/services disabled

---

## 13. Dependency & Supply Chain Security

### Rules

**DEPS-01 — Verify packages before installing.**
Check download counts, maintainer reputation, last update date, and known vulnerabilities before adding any dependency.

**DEPS-02 — Always use latest secure versions.**
Opt for newer, more secure package versions. Pin exact versions in lock files.

**DEPS-03 — Audit after every build.**
Run `npm audit fix` (or language equivalent) after every build. Address critical vulnerabilities immediately.

**DEPS-04 — SRI for CDN resources.**
Use Subresource Integrity (SRI) hashes for any scripts or styles loaded from CDNs.

```html
<script src="https://cdn.example.com/lib.js"
  integrity="sha384-abc123..."
  crossorigin="anonymous"></script>
```

**DEPS-05 — Sign and verify packages.**
Lock versions, verify integrity hashes, and audit the dependency tree regularly.

### Supply Chain Attack Vectors

| Vector | Description | Prevention |
|--------|-------------|------------|
| Typosquatting | `lodsah` instead of `lodash` | Double-check package names |
| Dependency confusion | Internal package name published publicly | Use scoped packages, configure registries |
| Malicious maintainer | Compromised or rogue package owner | Monitor packages, use lockfiles |
| Build script injection | Malicious postinstall scripts | Review install scripts, use `--ignore-scripts` |

### Supply Chain Checklist

- [ ] All dependencies pinned to exact versions
- [ ] `npm audit` (or equivalent) runs in CI
- [ ] Critical vulnerabilities block deployment
- [ ] CDN resources use SRI hashes
- [ ] No unused dependencies in the tree
- [ ] Package lock file committed and reviewed
- [ ] Typosquatting check on new dependencies
- [ ] Install scripts reviewed for new packages

---

## 14. Logging, Monitoring & Error Handling

### Rules

**LOG-01 — Log critical actions.**
Deletions, role changes, payments, exports, login attempts, and permission changes must all be logged.

**LOG-02 — Build real account deletion flows.**
GDPR and similar regulations require proper data deletion. Implement compliant deletion flows.

**LOG-03 — Review permissions server-side.**
UI-level permission checks are cosmetic. All enforcement must happen server-side.

### What to Log

| Event | Minimum Fields |
|-------|---------------|
| Login attempt (success/fail) | timestamp, user_id, IP, user_agent, success |
| Permission change | timestamp, actor_id, target_id, old_role, new_role |
| Data deletion | timestamp, actor_id, resource_type, resource_id |
| Payment event | timestamp, user_id, amount, status, payment_id |
| Export / download | timestamp, user_id, resource_type, record_count |
| Auth failure | timestamp, IP, attempted_user, failure_reason |
| Config change | timestamp, actor_id, setting, old_value, new_value |

### What NOT to Log

Never log: passwords (even hashed), full credit card numbers, SSNs/national IDs, session tokens, API keys, JWT secrets, raw PII that isn't necessary for the audit trail.

### Structured Log Format

```json
{
  "timestamp": "2026-03-17T10:30:00Z",
  "level": "warn",
  "event": "auth_failure",
  "correlation_id": "req-abc123",
  "ip": "203.0.113.42",
  "attempted_user": "user@example.com",
  "reason": "invalid_password",
  "attempt_count": 3
}
```

### Logging Checklist

- [ ] All security events logged with structured format
- [ ] No sensitive data in logs (passwords, tokens, PII)
- [ ] Log correlation IDs for request tracing
- [ ] Alerting configured for anomalies
- [ ] Account deletion flow is GDPR/CCPA compliant
- [ ] Logs stored securely with appropriate retention
- [ ] Log access restricted to authorized personnel
- [ ] Log tampering detection in place

---

## 15. Agentic AI Security

> Based on OWASP Agentic AI Security 2026.

When building or reviewing AI agent systems, these are the top 10 risks:

| Risk | Description | Mitigation |
|------|-------------|------------|
| **ASI01: Goal Hijack** | Prompt injection alters agent objectives | Input sanitization, goal boundaries, behavioral monitoring |
| **ASI02: Tool Misuse** | Tools used in unintended ways | Least privilege, fine-grained permissions, validate all I/O |
| **ASI03: Privilege Abuse** | Credential escalation across agents | Short-lived scoped tokens, identity verification |
| **ASI04: Supply Chain** | Compromised plugins/MCP servers | Verify signatures, sandbox, allowlist plugins |
| **ASI05: Code Execution** | Unsafe code generation/execution | Sandbox execution, static analysis, human approval gates |
| **ASI06: Memory Poisoning** | Corrupted RAG/context data | Validate stored content, segment by trust level |
| **ASI07: Agent Comms** | Spoofing between agents | Authenticate, encrypt, verify message integrity |
| **ASI08: Cascading Failures** | Errors propagate across systems | Circuit breakers, graceful degradation, isolation |
| **ASI09: Trust Exploitation** | Social engineering via AI | Label AI content, user education, verification steps |
| **ASI10: Rogue Agents** | Compromised agents acting maliciously | Behavior monitoring, kill switches, anomaly detection |

### Agent Security Checklist

- [ ] All agent inputs sanitized and validated
- [ ] Tools operate with minimum required permissions
- [ ] Credentials are short-lived and scoped
- [ ] Third-party plugins verified and sandboxed
- [ ] Code execution happens in isolated environments
- [ ] Agent communications authenticated and encrypted
- [ ] Circuit breakers between agent components
- [ ] Human approval required for sensitive operations
- [ ] Behavior monitoring for anomaly detection
- [ ] Kill switch available for all agent systems
- [ ] AI-generated content clearly labeled

---

## 16. PII & Sensitive Data Handling

### Categories of PII to Protect

| Category | Examples |
|----------|---------|
| Financial | Credit card numbers, bank routing numbers, CVVs, expiry dates |
| Identity | SSNs, passport numbers, driver's license numbers |
| Contact | Email addresses, phone numbers, mailing addresses |
| Health | Medical license numbers, insurance member IDs, dates of birth |
| Technical | API keys, IP addresses, session tokens |

### Rules

**PII-01 — Detect and redact before logging.**
Scan all log output for PII patterns (credit cards, SSNs, emails, API keys) and redact before writing.

**PII-02 — Mask in UI.**
Show only last 4 digits of card numbers. Display SSN as `***-**-1234`. Mask phone numbers as `***-***-4567`.

**PII-03 — Minimize collection and transmission.**
Only collect PII you actually need. Process locally where possible. Transmit only over TLS.

**PII-04 — Field-level encryption.**
Encrypt sensitive database columns (SSN, card numbers) at the field level, not just disk-level encryption.

**PII-05 — Data retention and deletion.**
Define retention policies. Delete PII you no longer need. Support right-to-deletion (GDPR Article 17).

### PII Detection Patterns

| Category | Detection Method |
|----------|-----------------|
| Credit card | Luhn-validated 13–19 digit sequences |
| SSN | 3-2-4 digit groups (NNN-NN-NNNN) |
| API key | Provider prefix patterns (sk-*, ghp_*, AKIA*) |
| Email | Standard email format validation |
| Phone | US/international phone number patterns |
| IP address | IPv4 dotted-quad format |
| Address | Street + city/state/zip patterns |

### PII Checklist

- [ ] PII never appears in logs
- [ ] Sensitive data masked in UI
- [ ] Field-level encryption on sensitive database columns
- [ ] Data retention policy defined and enforced
- [ ] Right-to-deletion flow implemented and tested
- [ ] PII transmission always over TLS
- [ ] Minimum necessary PII collected
- [ ] PII inventory maintained (what, where, why)

---

## 17. Payment & Financial Security

### Rules

**PAY-01 — Verify webhook signatures.**
Always verify webhook signatures before processing payment data. Use the payment provider's official SDK.

**PAY-02 — Use established payment providers.**
Use Stripe or equivalent PCI-compliant provider. Never handle raw card data in your application.

**PAY-03 — Server-side amount validation.**
Never trust the client for payment amounts. Calculate totals server-side and verify against the payment intent.

**PAY-04 — Idempotency keys.**
Use idempotency keys for all payment operations to prevent duplicate charges.

**PAY-05 — Use the latest API version.**
Always use the latest stable API version from your payment provider. Older versions may have known vulnerabilities.

### Payment Checklist

- [ ] Webhook signatures verified using provider SDK
- [ ] No raw card data handled by the application
- [ ] Payment amounts calculated and verified server-side
- [ ] Idempotency keys used for all payment operations
- [ ] Payment events logged (amount, status, payment_id)
- [ ] Refund flows tested end-to-end
- [ ] PCI compliance verified with provider
- [ ] Test and production payment keys fully separated
- [ ] Financial amounts stored as DECIMAL, never FLOAT

---

## 18. Security Headers Reference

Include these headers in all HTTP responses:

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self' https://api.yourdomain.com; frame-ancestors 'none'; base-uri 'self'; form-action 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Referrer-Policy: strict-origin-when-cross-origin
Cache-Control: no-store
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

| Header | Purpose |
|--------|---------|
| `Strict-Transport-Security` | Forces HTTPS for all future requests |
| `Content-Security-Policy` | Controls which resources can load (XSS prevention) |
| `X-Content-Type-Options` | Prevents MIME type sniffing |
| `X-Frame-Options` | Prevents clickjacking via iframes |
| `Referrer-Policy` | Controls referrer information sent to other sites |
| `Cache-Control` | Prevents caching of sensitive pages |
| `Permissions-Policy` | Restricts browser feature access (camera, mic, location) |

### Headers Checklist

- [ ] HSTS configured with `includeSubDomains`
- [ ] CSP deployed and tested
- [ ] No `unsafe-eval` in CSP script-src
- [ ] `X-Content-Type-Options: nosniff` on all responses
- [ ] `X-Frame-Options: DENY` (or CSP frame-ancestors)
- [ ] `Referrer-Policy` set
- [ ] `Cache-Control: no-store` on sensitive pages
- [ ] `Permissions-Policy` restricting unused features

---

## 19. Appwrite Security Guide

> Appwrite-specific security configuration and best practices.
> Reference: [appwrite.io/docs/advanced/security](https://appwrite.io/docs/advanced/security)

### Compliance

Appwrite Cloud is compliant with GDPR, CCPA, HIPAA, PCI, and SOC 2 Type I. Appwrite implements centralized IAM, Infrastructure as Code for production changes, AES encryption at rest, TLS/SSL in transit, regular key rotation, and third-party penetration testing.

### Authentication & Password Security

**AW-AUTH-01 — Argon2 password hashing.**
Appwrite hashes all passwords with Argon2 (winner of the Password Hashing Competition), combined with salting, adjustable work factors, and memory hardness. Imported users with non-Argon2 hashes are automatically re-hashed on first successful sign-in.

**AW-AUTH-02 — Password history.**
Enable password history in Auth > Security to prevent users from reusing recent passwords. Supports remembering up to 20 previous passwords.

**AW-AUTH-03 — Password dictionary.**
Enable the password dictionary check in Auth > Security to block passwords matching the 10,000 most common passwords. Combined with rate limits, this significantly reduces brute-force success.

**AW-AUTH-04 — Personal data in passwords.**
Disallow passwords containing the user's name, email, or phone number. Enable in Auth > Security > Personal data.

**AW-AUTH-05 — Multi-factor authentication (MFA).**
Appwrite supports MFA with TOTP authenticator apps, email OTP, and phone SMS as second factors. Always generate and display recovery codes before enabling MFA. Users need more than one verified factor before enforcement activates.

**AW-AUTH-06 — Session limits.**
Configure maximum active sessions per user in Auth > Security (default 10, max 100). Sessions exceeding the limit automatically delete the oldest session. Keep sessions active only as long as needed.

**AW-AUTH-07 — Session alerts.**
Enable email alerts for new session creation in Auth > Security > Session alerts. Users are notified when a new session is created on their account, excluding Magic URL, Email OTP, and OAuth2 logins (which already verify access).

**AW-AUTH-08 — Single Client SDK instance.**
Maintain exactly one instance of the Appwrite Client SDK in your app to avoid conflicting session data. Session persistence varies by platform: Web uses secure session cookies (falls back to localStorage), Flutter uses Application Documents, Apple uses UserDefaults, Android uses SharedPreferences.

### Permissions & Access Control

**AW-PERM-01 — Permissions model.**
Appwrite enforces permissions at the resource level (database, table, row, bucket, file). Permissions are enforced for Client SDKs and Server SDKs using JWT, but are bypassed when using a Server SDK with an API key. Always design with this distinction in mind.

**AW-PERM-02 — Default permissions.**
Resources created via Server SDK or Console without explicit permissions are empty (no one can access). Resources created via Client SDK without explicit permissions grant the creator read, update, and delete access by default.

**AW-PERM-03 — Permission types and roles.**
Use the `Permission` class with `read()`, `create()`, `update()`, `delete()`, and `write()` (alias for create+update+delete). Assign to roles: `Role.any()`, `Role.guests()`, `Role.users()`, `Role.user([ID])`, `Role.team([ID])`, `Role.team([ID], [ROLE])`, `Role.member([ID])`, `Role.label([ID])`.

**AW-PERM-04 — Avoid `Role.any()` on mutable resources.**
Using `Role.any()` for write/update/delete permissions on sensitive data is a critical security risk. Reserve `Role.any()` for read-only access on genuinely public data. All mutable operations should be scoped to specific users, teams, or roles.

**AW-PERM-05 — Row-level security.**
Enable Row Security in table settings for per-row permission enforcement. Without it, table-level permissions apply uniformly. With it enabled, grant only `CREATE` permission at the table level and assign row-specific permissions on creation.

**AW-PERM-06 — Memberships privacy.**
Configure which membership details are private in Auth > Security > Memberships privacy. Fields that can be hidden: `userName`, `userEmail`, and `mfa` status.

### API Key Security

**AW-KEY-01 — Least-privilege scopes.**
Grant only the scopes your API key needs. API keys bypass user-level permissions entirely — a leaked key with broad scopes is a full project compromise.

**AW-KEY-02 — Never expose API keys in client code.**
API keys are for Server SDKs and CLI only. Client SDKs authenticate through user sessions. Project IDs and endpoints are safe to include in client code; API keys are not.

**AW-KEY-03 — Use dynamic (ephemeral) API keys.**
For Appwrite Functions, use dynamic API keys which are automatically generated per execution, short-lived, and scoped. This eliminates manual key management and reduces exposure risk. Fall back to standard keys only when third-party services require long-lived keys.

**AW-KEY-04 — JWT for server-side user context.**
When your backend needs to act on behalf of a user, use JWT authentication (15-minute expiry) instead of API keys. JWTs respect user permissions. API keys bypass them. Discard the JWT client after each request.

### Encryption

**AW-ENC-01 — Storage encryption.**
Enable bucket-level encryption in bucket Settings > Security settings > Encryption. Files under 20MB are encrypted with AES-128-GCM. Enable this for all buckets containing sensitive data.

**AW-ENC-02 — Database column encryption.**
Enable encryption on text columns (varchar, text, mediumtext, longtext) when creating columns. Encrypted with AES-128-GCM. Note: encrypted columns cannot be queried — design your data model accordingly.

**AW-ENC-03 — HTTPS enforcement.**
Appwrite Cloud enforces HTTPS on all endpoints with `Strict-Transport-Security` headers. For self-hosted: always configure TLS certificates and enforce HTTPS. Appwrite supports TLS 1.2 and TLS 1.3 with automatic certificate management.

### Abuse Protection

**AW-ABUSE-01 — Rate limiting.**
Appwrite applies built-in rate limits on API endpoints to prevent brute-force and DoS attacks. Check each route's documentation for specific limits.

**AW-ABUSE-02 — Platform allowlisting (CORS).**
Appwrite rejects requests from domains not registered as platforms. Add your app's domain in Overview > Platforms > Add platform. This prevents JavaScript from unknown domains from accessing your project.

**AW-ABUSE-03 — DDoS protection.**
Appwrite Cloud includes always-on DDoS protection across Network (L3), Transport (L4), and Application (L7) layers. Protects against UDP/ICMP/TCP floods, DNS reflection, SYN floods, BGP attacks, and ping-of-death. Traffic monitoring detects and blocks suspicious patterns.

### Backups & Disaster Recovery

**AW-BACKUP-01 — Encrypted backups.**
All Appwrite database backups are encrypted in transit (TLS) and at rest. Backups are hot (zero-downtime) and stored in a geographically separate location from the primary data center.

**AW-BACKUP-02 — Automated backup policies.**
Configure automated daily backups (stored 7 days on Pro plan). Manual backups can be triggered at any time for pre-deployment snapshots. Scale/Enterprise plans offer custom schedules, retention periods, and geographic preferences.

**AW-BACKUP-03 — Test your restores.**
Having backups is useless without testing restoration. Regularly verify that backups can be successfully restored and that data integrity is maintained.

### Self-Hosting Security

**AW-SELF-01 — Production hardening checklist.**
For self-hosted Appwrite: enable HTTPS with TLS certificates, configure firewalls and security groups, implement proper authentication, set up log management, configure automated backups, and keep Appwrite updated with latest releases.

**AW-SELF-02 — Environment separation.**
Use one Appwrite project per environment (dev, staging, production) with strict secret rotation between environments.

### Appwrite Security Checklist

- [ ] Password history enabled (up to 20 previous passwords)
- [ ] Password dictionary check enabled
- [ ] Personal data in passwords disallowed
- [ ] MFA enabled with recovery codes generated
- [ ] Session limits configured (default 10)
- [ ] Session alerts enabled for new login notifications
- [ ] Single Client SDK instance maintained per app
- [ ] Row Security enabled on all sensitive tables
- [ ] `Role.any()` not used on mutable resources
- [ ] API key scopes follow least-privilege principle
- [ ] API keys never exposed in client-side code
- [ ] Dynamic API keys used for Appwrite Functions
- [ ] JWT used for server-side user context (not API keys)
- [ ] Storage bucket encryption enabled
- [ ] Sensitive database columns encrypted
- [ ] HTTPS enforced with HSTS
- [ ] App domains registered as platforms (CORS)
- [ ] Automated backup policies configured
- [ ] Backup restoration tested
- [ ] Memberships privacy configured (hide email/name if needed)

---

## 20. Language-Specific Security Quirks

> Each language has unique footguns. This is an entry point — go deeper for your specific stack.

### JavaScript / TypeScript

**Risks:** Prototype pollution, XSS, eval injection

```javascript
// UNSAFE: Prototype pollution
Object.assign(target, userInput)
// SAFE: Use null prototype or validate keys
Object.assign(Object.create(null), validated)
```

**Watch for:** `eval()`, `innerHTML`, `document.write()`, `__proto__`, prototype chain manipulation

### Python

**Risks:** Pickle deserialization RCE, format string injection, shell injection

```python
# UNSAFE: Pickle RCE
pickle.loads(user_data)
# SAFE: Use JSON
json.loads(user_data)
```

**Watch for:** `pickle`, `eval()`, `exec()`, `os.system()`, `subprocess` with `shell=True`

### Java

**Risks:** Deserialization RCE, XXE, JNDI injection

```java
// UNSAFE: Arbitrary deserialization
ObjectInputStream ois = new ObjectInputStream(userStream);
Object obj = ois.readObject();
// SAFE: Use JSON with specific class
ObjectMapper mapper = new ObjectMapper();
mapper.readValue(json, SafeClass.class);
```

**Watch for:** `ObjectInputStream`, `Runtime.exec()`, XML parsers without XXE protection, JNDI lookups

### Go

**Risks:** Template injection, goroutine data races

```go
// UNSAFE: Unescaped template output
template.HTML(userInput)
// SAFE: Let template escape
{{.UserInput}}
```

**Watch for:** `template.HTML()`, `unsafe` package, unchecked slice access, goroutine data races

### Ruby

**Risks:** Mass assignment, YAML deserialization, regex DoS

```ruby
# UNSAFE: YAML RCE
YAML.load(user_input)
# SAFE: Use safe_load
YAML.safe_load(user_input)
```

**Watch for:** `YAML.load`, `Marshal.load`, `eval`, `send` with user input, `.permit!`

### Rust

**Risks:** Unsafe blocks, FFI boundary issues, integer overflow in release

```rust
// CAUTION: Release integer overflow wraps to 0
let x: u8 = 255;
let y = x + 1; // Wraps to 0!
// SAFE: Use checked arithmetic
let y = x.checked_add(1).unwrap_or(255);
```

**Watch for:** `unsafe` blocks, FFI calls, `.unwrap()` on untrusted input, integer overflow in release builds

### C / C++

**Risks:** Buffer overflow, use-after-free, format string

```c
// UNSAFE: Buffer overflow
char buf[10]; strcpy(buf, userInput);
// SAFE: Bounds checking
strncpy(buf, userInput, sizeof(buf) - 1);
buf[sizeof(buf) - 1] = '\0';
```

**Watch for:** `strcpy`, `sprintf`, `gets`, `printf(userInput)`, pointer arithmetic, manual memory management

### Shell (Bash)

**Risks:** Command injection, word splitting, globbing

```bash
# UNSAFE: Unquoted variables
rm $user_file
# SAFE: Always quote
rm "$user_file"
```

**Watch for:** Unquoted variables, `eval`, backticks with user input, missing `set -euo pipefail`

### Swift

**Risks:** Force unwrapping crashes, Objective-C interop

```swift
// UNSAFE: Force unwrap on untrusted data
let value = jsonDict["key"]!
// SAFE: Safe unwrapping
guard let value = jsonDict["key"] else { return }
```

### Kotlin

**Risks:** Null safety bypass via Java interop, reflection

```kotlin
// UNSAFE: Platform type from Java (NPE if null)
val len = javaString.length
// SAFE: Explicit null check
val len = javaString?.length ?: 0
```

### Deep Security Analysis Mindset

For **any** language, think like a security researcher:

1. **Memory Model** — managed vs manual? GC pauses exploitable?
2. **Type System** — weak typing = type confusion attacks
3. **Serialization** — every language has its dangerous deserializer
4. **Concurrency** — race conditions, TOCTOU, atomicity failures
5. **FFI Boundaries** — native interop is where type safety breaks
6. **Standard Library** — historic CVEs in std libs (Python urllib, Java XML)
7. **Package Ecosystem** — typosquatting, dependency confusion, malicious packages
8. **Build System** — Makefile/gradle/npm script injection during builds
9. **Runtime Behavior** — debug vs release differences (Rust overflow, C++ assertions)
10. **Error Handling** — silent failures? Stack traces? Fail-open?

---

## 21. ASVS 5.0 Compliance Tiers

### Level 1 — All Applications

- Passwords minimum 12 characters
- Check against breached password lists
- Rate limiting on authentication endpoints
- Session tokens 128+ bits entropy
- HTTPS everywhere
- Basic input validation

### Level 2 — Sensitive Data

All Level 1 requirements, plus:

- MFA for sensitive operations
- Cryptographic key management
- Comprehensive security logging
- Input validation on all parameters
- Security headers configured
- Dependency auditing in CI

### Level 3 — Critical Systems

All Level 1 + 2 requirements, plus:

- Hardware security modules for key storage
- Threat modeling documentation
- Advanced monitoring and alerting
- Penetration testing validation
- Incident response plan tested
- Security architecture review

---

## 22. Master Checklists

### Pre-Commit Checklist

- [ ] No hardcoded secrets (API keys, passwords, tokens)
- [ ] No `console.log` in production code
- [ ] All inputs validated server-side
- [ ] Parameterized queries used (no SQL concatenation)
- [ ] Auth middleware applied to new endpoints
- [ ] Rate limiting configured on new endpoints
- [ ] Error messages don't expose internals
- [ ] CORS configured correctly
- [ ] No `eval()` or equivalent with user input
- [ ] File uploads validated by magic bytes

### Pre-Deployment Checklist

- [ ] `npm audit fix` run, no critical vulnerabilities
- [ ] Security headers configured (HSTS, CSP, X-Content-Type-Options)
- [ ] HTTPS enforced, no HTTP fallback
- [ ] Environment variables used for all secrets
- [ ] RLS enabled on all database tables
- [ ] Backups verified (test restore)
- [ ] Test and production environments separated
- [ ] Webhook signatures verified
- [ ] DDoS protection configured
- [ ] AI API cost caps set

### Code Review Security Pass

- [ ] Access control: deny-by-default, ownership verified at data layer
- [ ] Input handling: validated, sanitized, parameterized
- [ ] Output encoding: context-appropriate (HTML, JS, URL, CSS)
- [ ] Auth: present on every endpoint
- [ ] CSRF: tokens on all state-changing endpoints
- [ ] Secrets: none in code, URLs, logs, or client-side bundles
- [ ] Errors: fail-closed, generic messages, logged with context
- [ ] Dependencies: audited, pinned, up-to-date
- [ ] File uploads: validated, renamed, stored outside webroot
- [ ] Database: RLS enabled, least-privilege roles, FK constraints

### Incident Response Checklist

- [ ] Rotate all potentially compromised secrets immediately
- [ ] Revoke affected sessions and tokens
- [ ] Assess scope of data exposure
- [ ] Notify affected users per regulatory requirements
- [ ] Document the incident and root cause
- [ ] Update `tasks/lessons.md` with the pattern
- [ ] Implement prevention measures
- [ ] Verify fix with penetration testing
- [ ] Review and update this playbook

---

> **Last updated:** March 2026
> **Sources:** OWASP Top 10:2025, OWASP ASVS 5.0, OWASP Agentic AI Security 2026, VibeSec Secure Coding Guide, Supabase Postgres Best Practices, Appwrite Security Documentation
