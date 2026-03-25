# Activity Diagrams

> Business processes and workflows. Update when adding new processes.

```mermaid
%% Add activity diagrams as workflows are built
%% Example checkout flow:
%% flowchart TD
%%     Start([Start]) --> SelectItems
%%     SelectItems --> ReviewCart
%%     ReviewCart --> ChoosePayment{Payment Method}
%%     ChoosePayment -->|Stripe| StripeCheckout
%%     ChoosePayment -->|PayPal| PayPalCheckout
%%     ChoosePayment -->|TNG| TNGCheckout
%%     StripeCheckout --> Confirmation
%%     PayPalCheckout --> Confirmation
%%     TNGCheckout --> Confirmation
%%     Confirmation --> End([End])
```
