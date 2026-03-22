# Conventions

## Files
Components: `PascalCase.tsx` · Utils/hooks: `camelCase.ts` · Actions: `actions/[domain].ts` · Tests: `[name].test.ts` · Next.js specials: lowercase. Target <200 lines, max 300. One component per file. No barrel files. Import order: externals → `@/lib` → `@/components` → `@/types` → styles.

## TypeScript
`interface` for extendable shapes · `type` for unions · Never `any` → `unknown` + narrow · Explicit return types · `as const` for literals · Appwrite docs extend `Models.Document`

## Error Handling
Custom `AppError` from `lib/errors.ts` · `redirect()`/`notFound()` OUTSIDE try/catch or use `unstable_rethrow()` · Fail closed · Generic client messages, detailed server logs

## RSC Boundaries
Server Components default · `"use client"` only for hooks/events/browser APIs · No async client components · Serialisable props only (no Date/Map/Set/functions except Server Actions) · Convert Appwrite dates to ISO strings

## Design Standards (Production-Grade, Zero AI Slop)

### Research Before Design
Before building ANY interface, research 3-5 real production examples. Study how Linear, Vercel, Stripe, Notion, Apple design similar components. Use WebSearch/Firecrawl to pull real references. Never design from imagination alone.

### Accessibility (Non-Negotiable)
- WCAG 2.1 AA minimum on every component
- Keyboard navigation: every interactive element reachable via Tab, operable via Enter/Space
- Focus indicators: visible, high-contrast, never removed (`outline: none` is banned without replacement)
- Color contrast: 4.5:1 for normal text, 3:1 for large text, 3:1 for UI components
- Screen reader: semantic HTML first, `aria-*` only when native semantics insufficient
- Touch targets: minimum 44x44px on mobile
- Reduced motion: `prefers-reduced-motion` respected on all animations
- Skip links on every page
- Form labels always visible (no placeholder-only inputs)
- Error messages associated with inputs via `aria-describedby`

### Typography
- Maximum 2 typefaces per project (1 preferred)
- Clear hierarchy: display → heading → subheading → body → caption
- Line height: 1.5 for body, 1.2-1.3 for headings
- Measure (line length): 45-75 characters for body text
- No font-size below 14px for body text (16px preferred)
- Use `font-variant-numeric: tabular-nums` for data/numbers

### Spacing & Layout
- Base unit: 4px. All spacing is multiples of 4px (4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 96)
- Consistent padding within component families
- Content width: max 1280px for pages, 640px for text-heavy content
- Grid: 12-column for desktop, stack for mobile. No arbitrary breakpoints.
- Whitespace is a design tool — use it intentionally, not by accident

### Color
- Semantic tokens ONLY (`bg-primary`, `text-muted-foreground`) — never raw hex/rgb
- Meaningful color usage: not decorative. Every color communicates something.
- Dark mode from day one (shadcn handles this via CSS variables)
- Status colors: destructive (errors), warning (caution), success (confirmation) — consistent across app
- Never rely on color alone to convey information (use icons, text, patterns)

### Component States (EVERY interactive element)
- Default · Hover · Focus · Active · Disabled · Loading · Error · Empty · Success
- No state may be left undesigned
- Skeleton loading (never spinners for content areas)
- Error states with recovery actions ("Retry" not just "Error")
- Empty states with guidance ("Add your first project" not "No data")

### Motion & Animation
- Purposeful only — communicates state change, hierarchy, or spatial relationship
- Duration: 150-300ms for micro-interactions, 300-500ms for layout transitions
- Easing: `ease-out` for entrances, `ease-in` for exits, `ease-in-out` for movement
- `prefers-reduced-motion`: respect it. Provide reduced or no animation alternative.
- No animation on initial page load (perceived as slow)

### Design Principles Applied
- **Gestalt**: group related elements (proximity), make similar things look similar (similarity)
- **Fitts's Law**: important actions are large and close to the user's attention
- **Hick's Law**: reduce choices per view. One primary CTA. Progressive disclosure for complexity.
- **Jakob's Law**: follow platform conventions. Users expect your app to work like others.
- **Dieter Rams**: "Less, but better." Every element earns its place.

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
