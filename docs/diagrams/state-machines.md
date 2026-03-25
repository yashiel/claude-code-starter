# State Machine Diagrams

> Stateful entities: orders, payments, subscriptions. Update when status logic changes.

## Payment Status

```mermaid
stateDiagram-v2
    [*] --> Pending: Order created
    Pending --> Processing: Payment initiated
    Processing --> Completed: Payment successful (S)
    Processing --> Failed: Payment failed (F)
    Processing --> Unknown: Status unknown (U)
    Unknown --> Completed: Inquiry confirms success
    Unknown --> Failed: Inquiry confirms failure
    Unknown --> Unknown: Still processing (poll again)
    Completed --> Refunded: Refund processed
    Completed --> PartialRefund: Partial refund
    Failed --> Pending: Retry
    note right of Unknown: TNG 'U' status — NEVER mark as failed. Poll with inquiryPayment.
```

<!-- Add order status, subscription status, etc. as entities are created -->
