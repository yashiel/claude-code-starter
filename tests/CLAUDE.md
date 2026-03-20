# Testing

Vitest (unit/integration) · Playwright (e2e) · `npm test` · `npm run test:watch`

**Structure**: `tests/unit/` · `tests/integration/` · `tests/e2e/`

**Rules**: Arrange→Act→Assert · One assertion per test · Isolated · Mock Appwrite in unit · Wrap TanStack Query in `QueryClientProvider` · Worst-case + best-case always

**What to test**: zod schemas · utils · hooks · Server Actions (mock Appwrite) · webhook handlers (Stripe/PayPal/TNG sigs) · auth failures · payment edge cases (duplicate webhook, TNG `U` status, partial refund) · critical user flows (e2e)

**Adversarial**: null · empty · 10k items · Unicode · SQL in fields · expired session · concurrent requests · malformed webhook · amount manipulation
