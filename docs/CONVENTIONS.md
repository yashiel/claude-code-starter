# Conventions

## Files
Components: `PascalCase.tsx` · Utils/hooks: `camelCase.ts` · Actions: `actions/[domain].ts` · Tests: `[name].test.ts` · Next.js specials: lowercase. Target <200 lines, max 300. One component per file. No barrel files. Import order: externals → `@/lib` → `@/components` → `@/types` → styles.

## TypeScript
`interface` for extendable shapes · `type` for unions · Never `any` → `unknown` + narrow · Explicit return types · `as const` for literals · Appwrite docs extend `Models.Document`

## Error Handling
Custom `AppError` from `lib/errors.ts` · `redirect()`/`notFound()` OUTSIDE try/catch or use `unstable_rethrow()` · Fail closed · Generic client messages, detailed server logs

## RSC Boundaries
Server Components default · `"use client"` only for hooks/events/browser APIs · No async client components · Serialisable props only (no Date/Map/Set/functions except Server Actions) · Convert Appwrite dates to ISO strings

## shadcn/ui
CLI only: `npx shadcn@latest add` · Run `docs` before using · Semantic tokens (`bg-primary`) never raw colours · `gap-*` not `space-*` · `size-*` for equal dims · Forms: `FieldGroup`+`Field` · Icons: `data-icon` no sizing · `Skeleton` for loading · `toast()` from sonner · `Alert` for callouts · `Empty` for empty states · Full Card composition

## Data Fetching
Server Components → reads · Server Actions → writes (zod validation, `revalidatePath`) · Route Handlers → webhooks/external only · TanStack Query → client polling only

## Payments (full ref: docs/PAYMENTS.md)
**Stripe**: Checkout Sessions · webhook `constructEvent()` with raw body · `STRIPE_SECRET_KEY` server-only
**PayPal**: Orders v2 create/capture · `@paypal/react-paypal-js` buttons · OAuth2 token cached with TTL · `PAYPAL_CLIENT_SECRET` server-only
**TNG**: Server-to-server RSA256 signed · amount STRING cents ("10000"=RM100) · `isCashierPayment:true` · `customerBelongsTo:"TNG"` · Status: S=success, A=redirect, F=fail, U=poll(NEVER mark failed) · All keys server-only

## Git
Branches: `feat/` `fix/` `refactor/` `docs/` `chore/` · Commits: `type(scope): description` · Pre-commit: `lint && tsc && test` · PRs: <400 lines, what/why/test

## Testing
Unit: zod schemas, utils, hooks · Integration: Server Actions, webhook handlers · E2E: Playwright critical flows · Arrange→Act→Assert · Mock Appwrite in unit tests · Worst-case + best-case · Adversarial: null, empty, 10k, Unicode, SQL injection, auth failure, payment edge cases
