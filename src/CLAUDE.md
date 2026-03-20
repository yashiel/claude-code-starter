# src/ Conventions

Server Components default · `"use client"` only for hooks/events/browser · One component per file, named export · Never async client components · `node-appwrite` server only · `appwrite` browser only · All Appwrite IDs in `lib/appwrite/config.ts`

Import order: externals → `@/lib` → `@/components` → `@/types` → styles

## Templates

**Server Component** — `const { databases } = await createSessionClient(); const { documents } = await databases.listDocuments(DATABASE_ID, COLLECTIONS.X);`

**Server Action** — `'use server'; const parsed = schema.safeParse(...); if (!parsed.success) return { error }; const { databases } = await createSessionClient(); try { doc = await databases.createDocument(...); } catch { return { error }; } revalidatePath(); redirect();` ← redirect OUTSIDE try/catch

**shadcn Form** — `'use client'; <form action={serverAction}><FieldGroup><Field><FieldLabel/><Input name="x"/></Field></FieldGroup><Button type="submit"/></form>`

**Error Boundary** — `'use client'; export default function Error({ error, reset }) { return <Button onClick={reset}>Try again</Button> }`

**Stripe** — Server Action: `stripe.checkout.sessions.create({ mode:'payment', line_items, success_url, cancel_url })` → `redirect(session.url!)`

**PayPal** — Client: `<PayPalScriptProvider><PayPalButtons createOrder={fetch /api/paypal/create-order} onApprove={fetch /api/paypal/capture-order}/>`

**TNG** — Server Action: call `/v1/payments/pay` with RSA256 signed request → if `A` redirect to `actionForm.redirectionUrl` → if `U` return paymentId for polling
