# Component Request: [ComponentName]

> Copy this file to `docs/component-requests/[ComponentName].md` when no MiHCM component fits and composing/extending won't work. Never build a custom primitive — this handoff is the design team's build spec.

## Problem
What UI need this component solves. What the user is trying to do.

## Proposed Behavior
- Functional requirements (what it does)
- Interaction states (hover, focus, disabled, loading, error, empty)
- Responsive behavior (mobile → desktop)

## Props API (suggested)
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'default' \| 'outline' | 'default' | Visual style |

## Composition
Which existing MiHCM components it should build on internally.

## Accessibility
Keyboard nav, ARIA roles, focus management, screen reader behavior.

## Design References
Links to 3-5 real production examples (Linear, Stripe, Vercel, Notion, etc).

## Priority
Critical / High / Medium / Low — and why.

## Requested By
Date, project, and context for why this is needed.
