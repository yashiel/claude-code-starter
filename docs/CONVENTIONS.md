# Conventions

## Files
Components: `PascalCase.tsx` · Utils/hooks: `camelCase.ts` · Actions: `actions/[domain].ts` · Tests: `[name].test.ts` · Next.js specials: lowercase. Target 200-400 lines, max 800. One component per file. No barrel files. Import order: externals → `@/lib` → `@/components` → `@/types` → styles.

## Code Quality Invariants
- **Functions**: <50 lines. Single responsibility. Clear name = no comment needed.
- **Nesting**: max 4 levels deep. Extract early returns, guard clauses, helper functions.
- **Immutability**: always create new objects, never mutate. `const` by default. Spread/map over push/splice.
- **Error handling**: handle at every level. Never swallow silently. Fail closed.
- **Input validation**: validate at system boundaries (API routes, Server Actions, form submissions). Fail fast with Zod.
- **No magic values**: extract to named constants. `const MAX_RETRIES = 3` not `3`.
- **Pure functions**: prefer functions without side effects. Side effects at boundary, not logic.

## TypeScript
`interface` for extendable shapes · `type` for unions · Never `any` → `unknown` + narrow · Explicit return types · `as const` for literals

## Error Handling
Custom `AppError` from `lib/errors.ts` · `redirect()`/`notFound()` OUTSIDE try/catch · Fail closed · Generic client messages, detailed server logs · Every error handler logs context + correlation ID · Never expose stack traces

## RSC Boundaries
Server Components default · `"use client"` only for hooks/events/browser APIs · No async client components · Serialisable props only (no Date/Map/Set/functions except Server Actions) · Convert dates to ISO strings before client

## Design Standards (Production-Grade, Zero AI Slop)

### Research Before Design
Before building ANY interface, research 3-5 real production examples. Study Linear, Vercel, Stripe, Notion, Apple. Use WebSearch/Firecrawl. Never design from imagination alone.

### Accessibility (Non-Negotiable)
WCAG 2.1 AA minimum · Keyboard navigation (Tab/Enter/Space) · Visible focus indicators (never `outline: none` without replacement) · Color contrast 4.5:1 normal, 3:1 large · Semantic HTML first, `aria-*` when needed · Touch targets 44x44px · `prefers-reduced-motion` · Skip links · Visible form labels · Error messages via `aria-describedby`

### Typography
Max 2 typefaces (1 preferred) · Clear hierarchy: display → heading → subheading → body → caption · Line height 1.5 body, 1.2-1.3 headings · Line length 45-75 chars · Min 14px body (16px preferred) · `tabular-nums` for data

### Spacing & Layout
Base unit 4px (multiples: 4,8,12,16,20,24,32,40,48,64,80,96) · Consistent padding in component families · Max 1280px pages, 640px text · 12-column desktop, stack mobile · Intentional whitespace

### Color
Semantic tokens ONLY (`bg-primary` not raw hex) · Meaningful color usage · Dark mode from day one · Consistent status colors (destructive/warning/success) · Never color alone for information

### Component States (EVERY interactive element)
Default · Hover · Focus · Active · Disabled · Loading · Error · Empty · Success
Skeleton loading (never spinners for content) · Error states with recovery actions · Empty states with guidance

### Motion & Animation
Purposeful only · 150-300ms micro, 300-500ms layout · ease-out entrances, ease-in exits · Respect `prefers-reduced-motion` · No animation on initial load

### Design Principles
**Gestalt**: proximity, similarity · **Fitts's Law**: important = large + close · **Hick's Law**: fewer choices, progressive disclosure · **Jakob's Law**: follow conventions · **Dieter Rams**: "Less, but better."

## shadcn/ui
CLI only: `npx shadcn@latest add` · Run `docs` before using · Semantic tokens only · `gap-*` not `space-*` · `size-*` for equal dims · `Skeleton` for loading · `toast()` from sonner · `Alert` for callouts · Full Card composition

## Icons
Lucide React only (`lucide-react`). Consistent size per context. Always pair with text label or `aria-label`.

## Data Fetching
Server Components → reads · Server Actions → writes (zod validation, `revalidatePath`) · Route Handlers → webhooks/external APIs · TanStack Query → client polling/optimistic only

## Git
Branches: `feat/` `fix/` `refactor/` `docs/` `chore/` · Commits: `type(scope): description` · Pre-commit: `lint && tsc && test` · PRs: <400 lines, what/why/test

## Testing
Unit: zod schemas, utils, hooks · Integration: Server Actions, API routes · E2E: Playwright critical flows · Arrange→Act→Assert · Worst-case + best-case · Adversarial: null, empty, 10k, Unicode, SQL injection, auth failure
