# Security Quick Reference

> 24 rules. Full playbook: `docs/security-playbook.md` (22 sections, 100+ rules with IDs).
> Reference IDs in reviews: "Violates AUTH-02", "Fails INJ-01".

## Mindset
On every change: Can I bypass auth? Inject? Escalate? Leak data? Break it?

## Auth & Sessions
01 — Session max 7 days. JWT access tokens ≤15min. Refresh token rotation. (AUTH-01)
02 — Never AI-built auth. Use Appwrite Auth / battle-tested providers. (AUTH-02)
03 — API keys in `process.env` only. Never hardcode. (AUTH-03)

## Secure API Development
04 — Rotate secrets every 90 days. (CRYPTO-04)
05 — Verify all packages for security before installing. (DEPS-01)
06 — Always opt for newer, more secure versions. (DEPS-02)
07 — `npm audit fix` after every build. (DEPS-03)
08 — Parameterised queries always. Never concatenate. (INJ-01)

## API & Access Control
09 — Row-Level Security from day one. (DB-01, AW-PERM-05)
10 — Remove all `console.log` before production. (API-04)
11 — CORS restricted to allow-listed production domain. (API-02, AW-ABUSE-02)
12 — Validate all redirect URLs against allowlist. (API-03)
13 — Auth + rate limiting on every endpoint. (API-01)

## Data & Infrastructure
14 — Cap AI API costs in code and dashboard. (INFRA-02)
15 — DDoS protection via Cloudflare/Vercel edge. (INFRA-01)
16 — Lock storage per-user. Users access only their files. (UPLOAD-04, AW-PERM-01)
17 — Validate uploads by magic bytes, not extension. (UPLOAD-01)
18 — Verify webhook signatures before processing payments. (PAY-01, INFRA-05)

## Other Rules
19 — Server-side permissions only. UI checks are cosmetic. (AUTHZ-01, LOG-03)
20 — Log critical actions: deletions, role changes, payments, exports. (LOG-01)
21 — Build real account deletion flows. GDPR compliance. (LOG-02)
22 — Automate backups then test restores. (INFRA-06, AW-BACKUP-03)
23 — Test and production fully separate. (INFRA-03)
24 — Test webhooks never touch production systems. (INFRA-04)

## Pre-Commit (run every `/commit`)
No secrets · No console.log · Inputs validated · Queries parameterised · Auth on endpoints · Rate limiting · Generic errors · No eval · Upload magic bytes · Mass assignment blocked

## Pre-Deploy
npm audit clean · Security headers (HSTS/CSP) · HTTPS only · Env vars for secrets · RLS enabled · Webhook sigs verified · Env separated · DDoS protection · AI cost caps
