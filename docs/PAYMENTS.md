# Payments Integration Guide

> Reference document for integrating Stripe, PayPal, and TNG Digital eWallet.
> Read when: implementing payments, checkout flows, webhooks, refunds, or payment status handling.
> Last updated: [DATE]

## Payment Providers Overview

| Provider | Use Case | Currency | Integration Pattern |
|----------|----------|----------|-------------------|
| Stripe | International cards, subscriptions | Multi-currency | Checkout Sessions / Payment Element |
| PayPal | International trusted checkout | Multi-currency | Orders REST API + JS SDK buttons |
| TNG Digital | Malaysian eWallet (Touch 'n Go) | MYR only | Mini Program Open APIs (server-to-server + my.tradePay) |

## Architecture

```
src/lib/payments/
├── stripe/
│   ├── client.ts        # Stripe SDK init (server-only)
│   ├── checkout.ts      # Create Checkout Sessions
│   └── webhooks.ts      # Webhook signature verification
├── paypal/
│   ├── client.ts        # PayPal OAuth2 token + API client (server-only)
│   ├── orders.ts        # Create/Capture orders
│   └── webhooks.ts      # Webhook signature verification
├── tng/
│   ├── client.ts        # TNG API client with RSA256 signing (server-only)
│   ├── payments.ts      # /v1/payments/pay, inquiry, cancel
│   ├── refunds.ts       # /v1/payments/refund
│   ├── auth.ts          # /v1/authorizations/prepare, applyToken (auto-debit)
│   └── webhooks.ts      # Payment notification handler + signature validation
├── types.ts             # Unified PaymentResult, PaymentStatus types
└── router.ts            # Route to correct provider based on payment method

src/app/api/webhooks/
├── stripe/route.ts      # POST handler — verify Stripe signature
├── paypal/route.ts      # POST handler — verify PayPal webhook
└── tng/route.ts         # POST handler — verify TNG notification signature
```

## Environment Variables

```env
# Stripe
STRIPE_SECRET_KEY=sk_...           # Server-only
STRIPE_WEBHOOK_SECRET=whsec_...    # Server-only
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_...  # Client-safe

# PayPal
PAYPAL_CLIENT_ID=...               # Server-only (also used as NEXT_PUBLIC_ for JS SDK)
PAYPAL_CLIENT_SECRET=...           # Server-only — NEVER expose
PAYPAL_API_URL=https://api-m.sandbox.paypal.com  # Switch to api-m.paypal.com for production
NEXT_PUBLIC_PAYPAL_CLIENT_ID=...   # Client-safe (for PayPal JS SDK buttons)

# TNG Digital
TNG_CLIENT_ID=...                  # Server-only (Client-Id header)
TNG_PARTNER_ID=...                 # Server-only (partnerId in requests)
TNG_PRIVATE_KEY=...                # Server-only (RSA private key for request signing)
TNG_PUBLIC_KEY=...                 # Server-only (TNG's public key for response verification)
TNG_API_URL=https://open-api-tng.tngdigital.com.my  # Server-only
TNG_APP_ID=...                     # Mini Program app ID
```

IMPORTANT: `STRIPE_SECRET_KEY`, `PAYPAL_CLIENT_SECRET`, `TNG_PRIVATE_KEY` are NEVER prefixed with `NEXT_PUBLIC_`

---

## 1. Stripe Integration

### SDK & Packages
```bash
npm install stripe @stripe/react-stripe-js @stripe/stripe-js
```

### Server: Create Checkout Session (Server Action)
```typescript
'use server';
import Stripe from 'stripe';
import { redirect } from 'next/navigation';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function createStripeCheckout(priceId: string) {
  let session;
  try {
    session = await stripe.checkout.sessions.create({
      mode: 'payment',
      line_items: [{ price: priceId, quantity: 1 }],
      success_url: `${process.env.NEXT_PUBLIC_APP_URL}/payment/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${process.env.NEXT_PUBLIC_APP_URL}/payment/cancel`,
    });
  } catch (error) {
    return { error: 'Failed to create checkout session' };
  }
  redirect(session.url!);
}
```

### Server: Webhook Handler
```typescript
// app/api/webhooks/stripe/route.ts
import Stripe from 'stripe';
import { NextRequest, NextResponse } from 'next/server';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(request: NextRequest) {
  const body = await request.text();
  const signature = request.headers.get('stripe-signature')!;

  let event: Stripe.Event;
  try {
    event = stripe.webhooks.constructEvent(body, signature, process.env.STRIPE_WEBHOOK_SECRET!);
  } catch {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 400 });
  }

  switch (event.type) {
    case 'checkout.session.completed':
      // Update Appwrite document — mark order as paid
      break;
    case 'payment_intent.payment_failed':
      // Handle failure
      break;
  }

  return NextResponse.json({ received: true });
}
```

### Key Rules
- Use Checkout Sessions for simplest PCI-compliant flow
- Always verify webhook signatures — use RAW request body
- Idempotent webhook processing (check if order already fulfilled)
- Use latest API version (see stripe-best-practices skill)

---

## 2. PayPal Integration

### SDK & Packages
```bash
npm install @paypal/react-paypal-js
# Server-side: use fetch() to PayPal REST API (no server SDK needed for Orders v2)
```

### Server: OAuth2 Access Token
```typescript
// lib/payments/paypal/client.ts
const PAYPAL_API = process.env.PAYPAL_API_URL!;

export async function getPayPalAccessToken(): Promise<string> {
  const auth = Buffer.from(
    `${process.env.PAYPAL_CLIENT_ID}:${process.env.PAYPAL_CLIENT_SECRET}`
  ).toString('base64');

  const response = await fetch(`${PAYPAL_API}/v1/oauth2/token`, {
    method: 'POST',
    headers: {
      Authorization: `Basic ${auth}`,
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: 'grant_type=client_credentials',
  });

  const data = await response.json();
  return data.access_token;
}
```

### Server: Create & Capture Order (Route Handlers)
```typescript
// app/api/paypal/create-order/route.ts
export async function POST(request: NextRequest) {
  const { amount, currency = 'USD' } = await request.json();
  const accessToken = await getPayPalAccessToken();

  const response = await fetch(`${PAYPAL_API}/v2/checkout/orders`, {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${accessToken}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      intent: 'CAPTURE',
      purchase_units: [{ amount: { currency_code: currency, value: amount } }],
    }),
  });

  const order = await response.json();
  return NextResponse.json(order);
}

// app/api/paypal/capture-order/route.ts
export async function POST(request: NextRequest) {
  const { orderID } = await request.json();
  const accessToken = await getPayPalAccessToken();

  const response = await fetch(`${PAYPAL_API}/v2/checkout/orders/${orderID}/capture`, {
    method: 'POST',
    headers: { Authorization: `Bearer ${accessToken}`, 'Content-Type': 'application/json' },
  });

  const data = await response.json();
  // Update Appwrite document — mark order as paid
  return NextResponse.json(data);
}
```

### Client: PayPal Buttons
```tsx
'use client';
import { PayPalScriptProvider, PayPalButtons } from '@paypal/react-paypal-js';

export function PayPalCheckout({ amount }: { amount: string }) {
  return (
    <PayPalScriptProvider options={{ clientId: process.env.NEXT_PUBLIC_PAYPAL_CLIENT_ID! }}>
      <PayPalButtons
        createOrder={async () => {
          const res = await fetch('/api/paypal/create-order', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ amount }),
          });
          const order = await res.json();
          return order.id;
        }}
        onApprove={async (data) => {
          const res = await fetch('/api/paypal/capture-order', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ orderID: data.orderID }),
          });
          const details = await res.json();
          // Show success, redirect, etc.
        }}
      />
    </PayPalScriptProvider>
  );
}
```

### Key Rules
- Use Orders v2 REST API (not deprecated Payments v1)
- Server-side create + capture pattern (never client-only)
- OAuth2 token must be refreshed (short-lived) — cache with TTL
- Verify webhook signatures for async notifications
- `PAYPAL_CLIENT_SECRET` is server-only — NEVER expose

---

## 3. TNG Digital (Touch 'n Go eWallet) Integration

TNG uses a Mini Program platform (similar to Alipay/WeChat Mini Programs). The payment flow is server-to-server with RSA256 signed requests.

### API Fundamentals
- **Base URL**: Sandbox or production TNG Open API endpoint
- **Auth**: Every request requires `Client-Id`, `Request-Time`, and `Signature` headers
- **Signing**: RSA256 signature over `POST {path}\n{client-id}.{request-time}.{body}`
- **Response verification**: Validate response signature using TNG's public key
- **Currency**: MYR only
- **Amount format**: String in smallest unit (cents) — `"10000"` = RM100.00

### Payment Flow (Cashier Payment — Online/Mini Program)
```
1. Mini Program / Client → Your Server: "create order"
2. Your Server → TNG API: POST /v1/payments/pay
   - partnerId, appId, paymentRequestId, paymentAmount, paymentFactor: {isCashierPayment: true}
   - paymentNotifyUrl (webhook), paymentReturnUrl (redirect after payment)
   - extendInfo: {"customerBelongsTo": "TNG"}
3. TNG → Your Server: returns paymentId + actionForm.redirectionUrl (cashier page)
4. Your Server → Client: pass redirectionUrl
5. Client: calls my.tradePay (Mini Program) or redirects to cashier URL (web)
6. User completes payment on TNG cashier
7. TNG → Your Server: async notification to paymentNotifyUrl
8. TNG → Client: returns payment result
```

### Key APIs

| Endpoint | Purpose |
|----------|---------|
| `POST /v1/payments/pay` | Create payment order (returns cashier URL) |
| `POST /v1/payments/inquiryPayment` | Query payment status |
| `POST /v1/payments/cancel` | Cancel unpaid order |
| `POST /v1/payments/refund` | Refund (full or partial) |
| `POST /v1/authorizations/prepare` | Auto-debit setup (returns auth URL) |
| `POST /v1/authorizations/applyToken` | Exchange auth code for access token |

### Result Status Codes
| resultStatus | Meaning | Action |
|-------------|---------|--------|
| `S` | Success | Process as complete |
| `A` | Accepted | Redirect to cashier URL (actionForm.redirectionUrl) |
| `F` | Failed | Show error, do NOT retry with same paymentRequestId |
| `U` | Unknown | Poll with inquiryPayment, or wait for notification — do NOT mark as failed |

### Common Result Codes
- `SUCCESS` — payment/refund successful
- `ACCEPT` — payment accepted, redirect to cashier
- `PAYMENT_IN_PROCESS` — still processing (retry inquiry)
- `ORDER_NOT_EXIST` — payment not yet created (treat as failure)
- `USER_BALANCE_NOT_ENOUGH` — insufficient funds
- `RISK_REJECT` — rejected by risk engine
- `REFUND_WINDOW_EXCEED` — refund period expired

### Request Signing (RSA256)
```typescript
// lib/payments/tng/client.ts
import crypto from 'crypto';

function signRequest(method: string, path: string, clientId: string, requestTime: string, body: string): string {
  const content = `${method} ${path}\n${clientId}.${requestTime}.${body}`;
  const sign = crypto.createSign('SHA256');
  sign.update(content);
  return sign.sign(process.env.TNG_PRIVATE_KEY!, 'base64');
}

function verifyResponse(method: string, path: string, clientId: string, responseTime: string, body: string, signature: string): boolean {
  const content = `${method} ${path}\n${clientId}.${responseTime}.${body}`;
  const verify = crypto.createVerify('SHA256');
  verify.update(content);
  return verify.verify(process.env.TNG_PUBLIC_KEY!, signature, 'base64');
}
```

### Webhook (Payment Notification)
```typescript
// app/api/webhooks/tng/route.ts
export async function POST(request: NextRequest) {
  const body = await request.text();
  const signature = request.headers.get('signature');
  const clientId = request.headers.get('client-id');
  const responseTime = request.headers.get('response-time');

  // Verify TNG signature
  const isValid = verifyResponse('POST', '/your-notify-path', clientId!, responseTime!, body, extractSignatureValue(signature!));
  if (!isValid) {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 400 });
  }

  const notification = JSON.parse(body);
  if (notification.result.resultStatus === 'S') {
    // Update Appwrite document — mark order as paid
  }

  return NextResponse.json({ result: { resultCode: 'SUCCESS', resultStatus: 'S' } });
}
```

### Key Rules
- ALL TNG API calls are server-to-server — NEVER call from client
- Every request MUST be RSA256 signed — verify every response signature
- `paymentRequestId` + `partnerId` = idempotency key (TNG enforces this)
- Amount is in smallest unit as STRING: `"10000"` = RM100.00
- `extendInfo.customerBelongsTo` must be `"TNG"` for Mini Program payments
- `paymentFactor.isCashierPayment` must be `true` for online/Mini Program payments
- Handle `U` (Unknown) status carefully: poll with inquiryPayment, never mark as failed
- Refunds: partial and multiple supported, but total cannot exceed original payment
- TNG keys (`TNG_PRIVATE_KEY`, `TNG_PUBLIC_KEY`) are server-only — NEVER expose

---

## Unified Payment Router

```typescript
// lib/payments/router.ts
export type PaymentProvider = 'stripe' | 'paypal' | 'tng';

export async function createPayment(provider: PaymentProvider, amount: number, currency: string) {
  switch (provider) {
    case 'stripe':
      return createStripeCheckout(amount, currency);
    case 'paypal':
      return createPayPalOrder(amount, currency);
    case 'tng':
      if (currency !== 'MYR') throw new Error('TNG only supports MYR');
      return createTNGPayment(amount);
    default:
      throw new Error(`Unknown payment provider: ${provider}`);
  }
}
```

---

## Documentation Links

### Stripe
- [Stripe Docs](https://docs.stripe.com)
- [Checkout Sessions](https://docs.stripe.com/payments/checkout)
- [Webhooks](https://docs.stripe.com/webhooks)
- Use `stripe-best-practices` skill for API routing decisions

### PayPal
- [PayPal Developer](https://developer.paypal.com)
- [Orders v2 API](https://developer.paypal.com/docs/api/orders/v2/)
- [React PayPal JS](https://www.npmjs.com/package/@paypal/react-paypal-js)
- [Standard Payments SPA](https://developer.paypal.com/docs/checkout/standard/customize/single-page-app/)

### TNG Digital
- [TNG Mini Program Docs](https://miniprogram.tngdigital.com.my/docs)
- [Payment API /v1/payments/pay](https://miniprogram.tngdigital.com.my/docs/miniprogram_tngd/mpdev/xww2sz)
- [Refund API /v1/payments/refund](https://miniprogram.tngdigital.com.my/docs/miniprogram_tngd/mpdev/v1_refund)
- [API Fundamentals (signing)](https://miniprogram.tngdigital.com.my/docs/miniprogram_tngd/mpdev/rekqh-qm)
- [Auth Prepare (auto-debit)](https://miniprogram.tngdigital.com.my/docs/miniprogram_tngd/mpdev/v1_auth_prepare)
- [my.tradePay JSAPI](https://miniprogram.tngdigital.com.my/docs/miniprogram_tngd/mpdev/api_openapi_tradepay)
