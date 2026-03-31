# Payments Integration Guide

> Stripe, PayPal, TNG Digital. Read when implementing payments, webhooks, or refunds.

## Providers

| Provider | Currency | Pattern | Packages |
|----------|----------|---------|----------|
| Stripe | Multi | Checkout Sessions → webhook → update Appwrite | `stripe @stripe/react-stripe-js @stripe/stripe-js` |
| PayPal | Multi | Orders v2 REST + JS SDK buttons → capture → update Appwrite | `@paypal/react-paypal-js` (server: fetch) |
| TNG | MYR only | Server-to-server RSA256 → cashier redirect → async notification | None (crypto + fetch) |

## Architecture

```
src/lib/payments/{stripe,paypal,tng}/ → client.ts, checkout/orders/payments.ts, webhooks.ts
src/lib/payments/types.ts → unified PaymentResult, PaymentStatus
src/lib/payments/router.ts → route to provider by payment method
src/app/api/webhooks/{stripe,paypal,tng}/route.ts → POST handlers
```

## Env Vars (server-only unless prefixed NEXT_PUBLIC_)

**Stripe**: STRIPE_SECRET_KEY · STRIPE_WEBHOOK_SECRET · NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY
**PayPal**: PAYPAL_CLIENT_ID · PAYPAL_CLIENT_SECRET · PAYPAL_API_URL (sandbox: api-m.sandbox.paypal.com, prod: api-m.paypal.com) · NEXT_PUBLIC_PAYPAL_CLIENT_ID
**TNG**: TNG_CLIENT_ID · TNG_PARTNER_ID · TNG_PRIVATE_KEY · TNG_PUBLIC_KEY · TNG_API_URL · TNG_APP_ID

**CRITICAL**: STRIPE_SECRET_KEY, PAYPAL_CLIENT_SECRET, TNG_PRIVATE_KEY are NEVER NEXT_PUBLIC_

## 1. Stripe

**Flow**: Server Action → `stripe.checkout.sessions.create()` → redirect to `session.url` → webhook `checkout.session.completed` → verify sig with raw body → update Appwrite.
**Rules**: Checkout Sessions (simplest PCI) · Verify webhook with `constructEvent(rawBody, sig, secret)` · Idempotent processing · Latest API version · `redirect()` OUTSIDE try/catch.

## 2. PayPal

**Flow**: Client `<PayPalButtons>` → `/api/paypal/create-order` (OAuth2 token + Orders v2 create) → user approves → `/api/paypal/capture-order` → update Appwrite.
**Rules**: Orders v2 (not deprecated v1) · Server-side create + capture · OAuth2 token is short-lived (cache with TTL) · `PAYPAL_CLIENT_SECRET` server-only.

## 3. TNG Digital

**Fundamentals**: Server-to-server only · RSA256 signed requests · Headers: Client-Id, Request-Time, Signature · Signing: `POST {path}\n{clientId}.{requestTime}.{body}` · Verify responses with TNG public key · MYR only · Amount = STRING in cents ("10000" = RM100.00).

**Flow**: Server → `POST /v1/payments/pay` (partnerId, appId, paymentRequestId, paymentAmount, paymentFactor: {isCashierPayment: true}, extendInfo: {customerBelongsTo: "TNG"}) → status A → redirect to cashier → user pays → async notification → verify sig → update Appwrite.

**Key APIs**: `/v1/payments/pay` (create) · `/v1/payments/inquiryPayment` (status) · `/v1/payments/cancel` · `/v1/payments/refund` · `/v1/authorizations/prepare` (auto-debit) · `/v1/authorizations/applyToken`.

**Status Codes**: S = success · A = accepted (redirect to cashier) · F = failed (don't retry same ID) · **U = unknown (poll with inquiryPayment, NEVER mark as failed)**.

**Result Codes**: SUCCESS · ACCEPT · PAYMENT_IN_PROCESS (retry) · ORDER_NOT_EXIST · USER_BALANCE_NOT_ENOUGH · RISK_REJECT · REFUND_WINDOW_EXCEED.

**Rules**: All calls server-to-server · Every request RSA256 signed · paymentRequestId + partnerId = idempotency · isCashierPayment: true · customerBelongsTo: "TNG" · Handle U carefully · Refunds: partial OK, total ≤ original · All keys server-only.

## Unified Router

`createPayment(provider, amount, currency)` → switches to Stripe/PayPal/TNG. TNG throws if currency ≠ MYR.
