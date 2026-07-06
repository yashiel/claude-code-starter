# src/ Conventions

Server Components default · `"use client"` only for hooks/events/browser · One component per file, named export · Never async client components · Import order: externals → `@/lib` → `@/components` → `@/types` → styles

## Templates

**Server Component** — fetch data server-side, render directly. Use `Promise.all` for parallel fetches. Wrap in `<Suspense>` for streaming.

**Server Action** — `'use server'; const parsed = schema.safeParse(...); if (!parsed.success) return { error }; try { await db.create(...); } catch { return { error }; } revalidatePath(); redirect();` ← redirect OUTSIDE try/catch

## API Layer (lib/api/)
`lib/api/client.ts` — `openapi-fetch` `createClient<paths>()` (server-only, auth header from server env) · `lib/api/[domain].ts` — domain modules wrapping the client with `cache()` and error normalization (`AppError`) · `lib/auth/session.ts` — `getServerSession()` / `requireSession()` · `lib/schemas/` — Zod schemas (no `server-only`, shared client+server)
All `lib/api/*` files MUST start with `import 'server-only'`. Never import them from client components.

**Client Component** — `'use client'; export function MyForm() { return <form action={serverAction}>...</form> }`

**Error Boundary** — `'use client'; export default function Error({ error, reset }) { return <Button onClick={reset}>Try again</Button> }`
