# Conventions

## Files
Components: `PascalCase.tsx` · Utils/hooks: `camelCase.ts` · Actions: `actions/[domain].ts` · Stores: `stores/[domain].ts` · Tests: `[name].test.ts` · Next.js specials: lowercase. Target 200-400 lines, max 800. One component per file. No barrel files. Import order: externals → `@yashiel/*` → `@/lib` → `@/components` → `@/stores` → `@/types` → styles.

## Code Quality Invariants
- **Functions**: <50 lines. Single responsibility. Clear name = no comment needed.
- **Nesting**: max 4 levels deep. Extract early returns, guard clauses, helper functions.
- **Immutability**: always create new objects, never mutate. `const` by default. Spread/map over push/splice.
- **Error handling**: handle at every level. Never swallow silently. Fail closed.
- **Input validation**: validate at system boundaries (API routes, Server Actions, form submissions). Fail fast with Zod.
- **No magic values**: extract to named constants or use MiHCM tokens. Never hardcode colors, spacing, font sizes.
- **Pure functions**: prefer functions without side effects. Side effects at boundary, not logic.

## TypeScript
`interface` for extendable shapes · `type` for unions · Never `any` → `unknown` + narrow · Explicit return types · `as const` for literals

## Error Handling
Custom `AppError` from `lib/errors.ts` · `redirect()`/`notFound()` OUTSIDE try/catch · Fail closed · Generic client messages, detailed server logs · Every error handler logs context + correlation ID · Never expose stack traces

## RSC Boundaries
Server Components default · `"use client"` only for hooks/events/browser APIs · No async client components · Serialisable props only (no Date/Map/Set/functions except Server Actions) · Convert dates to ISO strings before client

## State Management
- **Zustand** for client-side state (UI state, filters, selections, form wizards)
- **Server state** via Server Components + TanStack Query for polling/optimistic
- **No prop drilling** — Zustand stores for cross-component state
- Store files in `src/stores/[domain].ts`

## Enforced Library Usage

Every library below is mandatory for its domain. Never build custom solutions when these exist.

### Forms — `react-hook-form` + `@hookform/resolvers` + `zod`
All forms use React Hook Form with Zod resolver. Pair with MiHCM `Form`, `Input`, `Select`, etc.
```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
const schema = z.object({ name: z.string().min(1), email: z.string().email() });
const form = useForm({ resolver: zodResolver(schema) });
```

### Tables — `@tanstack/react-table`
Headless table logic. Always render through MiHCM `DataTable` or `Table` components.
Never write custom sorting, filtering, or pagination logic — TanStack Table handles it.

### Virtualization — `@tanstack/react-virtual`
Any list/grid rendering 100+ items MUST use virtual scrolling. No exceptions.

### i18n — `next-intl`
All user-facing strings go through `next-intl`. No hardcoded display text. Use `useTranslations()` in client components, `getTranslations()` in server components.

### Dates — `date-fns` + `date-fns-tz`
All date manipulation uses `date-fns`. Timezone-aware operations use `date-fns-tz`. Never use `moment`, `dayjs`, or raw `Date` methods for formatting/manipulation.

### Data Visualization — `d3`
Charts, graphs, and custom visualizations use D3. Always bind MiHCM color tokens (`--color-primary`, `--color-accent`, etc.) for palette. Never use Chart.js or Recharts.

### Drag & Drop — `@dnd-kit/core`
Sortable lists, kanban, reorderable UI. Never use `react-beautiful-dnd` or custom drag event handlers.

### Command Palette — `cmdk`
Command menu / spotlight search. Integrates with MiHCM `Command` component.

### Rich Text — `tiptap`
Rich text editing for content creation. Never use Quill, Slate, or Lexical.

### Animation — `motion`
All transitions and layout animations use `motion` (formerly framer-motion). Use MiHCM motion tokens for duration values (`duration-micro`, `duration-medium`, `duration-long`). Respect `prefers-reduced-motion`.

### File Upload — `react-dropzone`
Drag-and-drop file uploads. Never write custom drag event handlers for file upload.

### Error Monitoring — `@sentry/nextjs`
Production error tracking. Captures unhandled errors, performance data, and custom events.

### Analytics — `posthog-js`
Product analytics and feature flags. Track user events, page views, and feature usage.

### Testing — `vitest` + `@testing-library/react` + `playwright`
- **Unit/Integration**: `vitest` + `@testing-library/react`. Arrange-Act-Assert pattern.
- **E2E**: `playwright` for critical user flows. Never use Cypress or Puppeteer.
- Test files: `[name].test.ts` (unit), `[name].spec.ts` (e2e).

### Emails — `@react-email`
Transactional email templates built with React components. Never build raw HTML email strings.

## MiHCM Design System (MANDATORY)

### Component Usage
ALL UI components must come from `@yashiel/mihcm-ui`. No exceptions. No other UI library.

```tsx
// CORRECT — subpath import
import { Button } from '@yashiel/mihcm-ui/Button';
import { Card } from '@yashiel/mihcm-ui/Card';
import { DataTable } from '@yashiel/mihcm-ui/DataTable';

// WRONG — barrel import
import { Button } from '@yashiel/mihcm-ui';
```

### Available Components (60+)
**Form**: Button, Input, Textarea, Checkbox, Switch, RadioGroup, Select, Label, Combobox, InputOTP, Slider, DatePicker, Form, SearchField
**Data Display**: Table, DataTable, Badge, StatusBadge, Avatar, AvatarGroup, Card, StatCard, Progress, Breadcrumb, Tag, Chart
**Feedback**: Alert, Toast (Sonner), Dialog, AlertDialog, Banner, EmptyState
**Navigation**: Tabs, Pagination, Accordion, Sidebar, MainSidebar, TopBar, TitleBar, NavigationMenu, Menubar
**Layout**: PageShell, SectionHeader, Separator, AspectRatio, Resizable, ScrollArea, Collapsible
**Overlay**: Sheet, Drawer, DropdownMenu, ContextMenu, Popover, HoverCard, Tooltip
**Content**: Text, RichTextEditor, Calendar, Carousel, Command, Logo, Skeleton, Toggle

### Design Tokens (use instead of hardcoded values)
**Colors**: `primary` (navy #003385), `accent` (orange #f16012), `destructive`, `success`, `warning`, `muted`, `background`, `foreground`. All via CSS variables.
**Spacing**: 4px base. Tailwind utilities: `p-1`(4px) through `p-24`(96px).
**Typography**: `caption`(12px), `label`(14px), `body-sm`(14px), `body`(16px), `title`(24px), `stat`(24px).
**Motion**: `instant`(0ms), `micro`(150ms), `short`(200ms), `medium`(300ms), `long`(500ms).
**Breakpoints**: `sm`(640px), `md`(768px), `lg`(1024px), `xl`(1280px), `2xl`(1536px).

### Icons
`@yashiel/mihcm-icons` only. 1,703 Lucide-based icons. PascalCase. Always pair with text label or `aria-label`.

### MiHCM MCP Tools (use before building)
- `mihcm_search_components` — find the right component
- `mihcm_get_component` — get usage details, props, examples
- `mihcm_list_tokens` — list all design tokens
- `mihcm_review_frontend_snippet` — validate code against design system rules
- `mihcm_check_contract` — contract validation
- `mihcm_check_client_server_boundary` — RSC boundary checking

## Design Standards (Production-Grade)

### Research Before Design
Before building ANY interface, research 3-5 real production examples. Study Linear, Vercel, Stripe, Notion, Apple. Use WebSearch/Firecrawl. Never design from imagination alone.

### Accessibility (Non-Negotiable)
WCAG 2.1 AA minimum · Keyboard navigation (Tab/Enter/Space) · Visible focus indicators (never `outline: none` without replacement) · Color contrast 4.5:1 normal, 3:1 large · Semantic HTML first, `aria-*` when needed · Touch targets 44x44px · `prefers-reduced-motion` · Skip links · Visible form labels · Error messages via `aria-describedby`

### Typography (MiHCM tokens)
Use MiHCM typography tokens (`text-caption`, `text-label`, `text-body-sm`, `text-body`, `text-title`, `text-stat`). Clear hierarchy. Line height 1.5 body, 1.2-1.3 headings. Line length 45-75 chars.

### Spacing & Layout (MiHCM tokens)
Base unit 4px. Use Tailwind utilities aligned with MiHCM spacing scale. Standard page gutters: `px-4 sm:px-6 lg:px-8`. Max 1280px pages, 640px text. 12-column desktop, stack mobile.

### Color (MiHCM semantic tokens ONLY)
`bg-primary` not raw hex · Meaningful color usage · Dark mode from day one · Status: `destructive`/`warning`/`success` · Never color alone for information

### Component States (EVERY interactive element)
Default · Hover · Focus · Active · Disabled · Loading · Error · Empty · Success
Skeleton loading (never spinners for content) · Error states with recovery actions · Empty states with guidance

### Motion & Animation (MiHCM motion tokens)
`duration-micro`(150ms) for micro, `duration-medium`(300ms) for layout · ease-out entrances, ease-in exits · Respect `prefers-reduced-motion` · No animation on initial load

### Design Principles
**Gestalt**: proximity, similarity · **Fitts's Law**: important = large + close · **Hick's Law**: fewer choices, progressive disclosure · **Jakob's Law**: follow conventions · **Dieter Rams**: "Less, but better."

### AI Slop Detection (run on every UI change)
- [ ] Would a design-conscious user trust this with their credit card?
- [ ] Clear visual hierarchy (one primary action per view)?
- [ ] All components from MiHCM design system?
- [ ] No hardcoded values (colors, spacing, fonts)?
- [ ] Every icon has text label or aria-label?
- [ ] Empty state designed with guidance?
- [ ] Error messages helpful and human?
- [ ] Loading state uses Skeleton?
- [ ] Transitions use MiHCM motion tokens?

## 8-Lens Persona Protocol

Run all 8 before writing code on any task.

### Checklist
- [ ] **System Architect** — fits system? data flow? boundaries? scale 10x/100x?
- [ ] **Software Engineer** — clean? SOLID? error handling? edge cases? types?
- [ ] **Database Engineer** — schema? indexes? queries? migrations reversible?
- [ ] **QA Engineer** — what breaks? null? empty? 10k? unicode? concurrent? auth bypass?
- [ ] **Designer** — MiHCM components used? no hardcoded values? production-grade? researched examples?
- [ ] **Creative Director** — typography via MiHCM tokens? color story? spacing rhythm?
- [ ] **UX Designer** — frictionless? cognitive load? feedback? error recovery? progressive disclosure?
- [ ] **Frontend Developer** — RSC correct? accessible? mobile-first? all states? performance?

### Priority by Task
| Task | Primary | Secondary |
|------|---------|-----------|
| Feature | Architect, Engineer, QA | All |
| UI | Designer, Creative Dir, UX, Frontend | QA |
| Bug fix | QA, Engineer | Architect |
| DB change | DB Engineer, Architect | QA |
| API | Architect, Engineer, QA | Security |
| Refactor | Architect, Engineer | QA |

## Data Fetching
Server Components → reads · Server Actions → writes (Zod validation, `revalidatePath`) · Route Handlers → webhooks/external APIs · TanStack Query → client polling/optimistic · Zustand → client UI state

## React Rules
No async client components · Serialisable props only across RSC boundary · Composition > booleans · `handleVerbNoun` · Avoid barrel files · `gap-*` not `space-*` · `size-*` for equal dims

## Git
Branches: `feat/` `fix/` `refactor/` `docs/` `chore/` · Commits: `type(scope): description` · Pre-commit: `lint && tsc && test` · PRs: <400 lines, what/why/test

## Testing
Unit: Zod schemas, utils, hooks, Zustand stores · Integration: Server Actions, API routes · E2E: Playwright critical flows · Arrange→Act→Assert · Worst-case + best-case · Adversarial: null, empty, 10k, Unicode, SQL injection, auth failure
