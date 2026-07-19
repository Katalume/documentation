# Subscription readiness

**Status:** implementation complete; commercial activation pending

**Runtime state:** disabled by safe-default feature flags

**Pricing state:** version 1 launch configuration

**Payment provider state:** Cashfree selected for the India-first adapter

Katalume may display its membership catalog while checkout is disabled, but it
must not create mandates or orders until every activation gate in this document
is closed. A browser redirect never grants paid access. The production beta
remains fully accessible until paid-entitlement enforcement is separately
enabled.

## Implemented membership model

- Free includes a stable, topic-balanced set of 60 problems: 30 Easy, 20 Medium
  and 10 Hard.
- Plus includes every current and future problem, Interview Tracks, premium
  Progress intelligence, and the premium Profile identity layer.
- Plus is offered weekly, monthly, or yearly.
- Lumus is the one-time lifetime tier and carries the same all-access benefits.
- Competitions remain Coming Soon and are outside the billing gate.
- Learn remains outside this rollout until its content owner completes the
  learning experience and the benefit split is reviewed.

| Offer key | Display name | Price | Collection |
|---|---|---:|---|
| `plus_weekly_in_v1` | Plus Weekly | ₹79 weekly | Cashfree periodic subscription |
| `plus_monthly_in_v1` | Plus Monthly | ₹249 monthly | Cashfree periodic subscription |
| `plus_yearly_in_v1` | Plus Yearly | ₹1,999 yearly | Cashfree periodic subscription |
| `lumus_lifetime_in_v1` | Lumus Lifetime | ₹4,999 once | Cashfree one-time order |

These are immutable version 1 offer snapshots in code, not permission for the
production account to charge. Changing a live price requires a new offer key.
The amounts and tax display still require owner and professional review before
activation.

## Design goals

- Support weekly, monthly, yearly, and lifetime offers in INR.
- Keep plan benefits and pricing independent from the payment provider.
- Make provider callbacks replay-safe, auditable, and recoverable.
- Grant access from Katalume's entitlement ledger, never from a browser claim.
- Allow an India-first provider now and a different or additional provider
  later without rewriting product authorization.
- Preserve a useful free tier and avoid locking learning history behind payment.

## Explicit non-goals for activation

- No discount, trial, coupon, proration, upgrade/downgrade, or family plan is
  included in version 1.
- No payment-method data is stored by Katalume.
- No claim is made that lifetime access means the service must operate forever.
- This document is engineering readiness, not tax or legal advice.

## Commercial model boundary

The product catalog should describe offers without embedding provider IDs in
application code.

| Concept | Example values | Rule |
|---|---|---|
| Product tier | `free`, `plus` | Determines the entitlement set |
| Billing cadence | `weekly`, `monthly`, `yearly`, `lifetime` | Lifetime is a one-time purchase, not a recurring subscription |
| Price version | immutable integer | Existing purchases retain the price/version they accepted |
| Currency | `INR` initially | Store minor units; never use floating point |
| Offer status | `draft`, `active`, `retired` | Retiring an offer must not revoke valid access |
| Provider mapping | provider + external plan/offer ID | Configuration/data, not authorization logic |

Each purchasable offer should have a stable internal `offerKey`, such as
`plus_monthly_in_v1`. The displayed amount, tax treatment, benefits, and
provider mapping are immutable once a real purchase references the offer.
Create a new version to change them.

Weekly plans can improve affordability but usually create more renewals,
support load, mandate failures, and involuntary churn. Keep the cadence in the
model, but decide whether to launch it only after provider capability and
unit-economics review.

## Source-of-truth rules

1. The payment provider is the source of truth for money movement.
2. Katalume is the source of truth for product entitlements.
3. A signed webhook or an authenticated server-to-server reconciliation may
   change billing state.
4. A checkout success page is informational only. It must never unlock access.
5. Duplicate and out-of-order events are expected.
6. Provider downtime must not immediately remove already-paid access.
7. Manual support grants must be separate, attributed, expiring where
   appropriate, and auditable.

## Production data model

All identifiers below are internal opaque IDs unless prefixed with
`provider`. The shipped version implements the customer, subscription,
purchase, webhook-event, and entitlement-grant subset. Payment/refund ledgers,
tax profiles, dead-letter automation, and support reconciliation remain
activation gates rather than claims about the current code.

### `BillingCustomer`

| Field | Purpose |
|---|---|
| `userId` | Unique Katalume user |
| `provider` | Adapter key, initially `cashfree` |
| `providerCustomerId` | Encrypted or access-restricted external reference |
| `billingEmail` | Invoice/contact address; separate from login changes |
| `taxProfile` | Optional legal name, country/state, postal code, GSTIN after validation |
| `createdAt`, `updatedAt` | Audit timestamps |

Unique index: `{ provider: 1, providerCustomerId: 1 }`. A user may have more
than one provider customer during migration, so do not make `userId` globally
unique without the provider dimension.

### `BillingOffer`

| Field | Purpose |
|---|---|
| `offerKey` | Stable public-safe internal key |
| `tier` | Entitlement tier |
| `cadence` | Weekly, monthly, yearly, or lifetime |
| `currency` | ISO currency, initially `INR` |
| `amountMinor` | Integer paise |
| `taxBehavior` | Inclusive, exclusive, or not applicable after professional review |
| `version` | Immutable commercial version |
| `benefitSetVersion` | Immutable entitlement definition |
| `providerMappings` | External plan/offer IDs by provider and environment |
| `status` | Draft, active, or retired |

Unique index: `{ offerKey: 1, version: 1 }`.

### `Subscription`

| Field | Purpose |
|---|---|
| `userId`, `billingCustomerId`, `offerId` | Ownership and accepted offer |
| `provider`, `providerSubscriptionId` | Reconciliation keys |
| `status` | Internal normalized state |
| `currentPeriodStart`, `currentPeriodEnd` | Access window |
| `cancelAtPeriodEnd`, `cancelledAt` | Cancellation semantics |
| `graceUntil` | Bounded recovery window |
| `latestProviderEventAt` | Out-of-order event protection |
| `version` | Optimistic concurrency |

Normalized states:

```text
pending -> active -> past_due -> active
                   -> grace -> expired
          active -> cancel_scheduled -> expired
pending -> failed
active|past_due|grace -> cancelled
```

Provider state strings must be retained in metadata for investigation but
translated through the adapter before product code sees them.

### `Purchase`

Lifetime access belongs in a separate one-time `Purchase` record. It contains
the accepted offer snapshot, provider order/payment IDs, amount/currency/tax
snapshot, status, captured/refunded timestamps, and reconciliation metadata.
The corresponding entitlement has no scheduled end but remains subject to the
published lifetime terms.

### `Payment` and `Refund`

Store provider references, internal owner, amount in minor units, currency,
normalized status, provider status, attempt number, failure category, captured
timestamp, and immutable offer/tax snapshots. Never store PAN, CVV, UPI PIN,
bank credentials, or a reusable mandate credential.

### `WebhookEvent`

| Field | Purpose |
|---|---|
| `provider`, `providerEventId` | Idempotency key |
| `payloadHash` | Detect conflicting replays |
| `signatureValid` | Verification result |
| `receivedAt`, `occurredAt` | Ordering and latency |
| `status` | Received, processing, processed, ignored, failed, dead-letter |
| `attempts`, `nextAttemptAt`, `lastErrorCode` | Retry control |
| `resourceRefs` | Redacted lookup IDs |

Unique index: `{ provider: 1, providerEventId: 1 }`.

Store the minimum payload needed for dispute and replay handling. Encrypt or
redact personal fields and apply a documented retention limit.

### `EntitlementGrant`

| Field | Purpose |
|---|---|
| `userId` | Access owner |
| `benefitSetVersion` | Immutable benefits |
| `sourceType`, `sourceId` | Subscription, purchase, promotion, support |
| `startsAt`, `endsAt` | Access interval; lifetime may have no `endsAt` |
| `status` | Scheduled, active, revoked, expired |
| `reason`, `actorId` | Manual action audit |

The effective entitlement is computed server-side from active grants. Cache it
briefly, invalidate on billing events, and fail to the last verified grant
during a short provider outage.

## Backend module boundary

Billing code lives behind this provider-neutral boundary:

```text
src/billing/
  billing.service.js
  entitlement.service.js
  providers/
    cashfree.adapter.js
```

The current adapter covers:

```text
createCheckout
cancelSubscription
verifyWebhook
```

Controllers must not call a provider SDK directly. Provider objects must not
leak into user or authorization models.

## Implemented API surface

All mutating endpoints require an authenticated user, CSRF protection where
applicable, distributed rate limits, and an idempotency key.

| Method and route | Purpose |
|---|---|
| `GET /api/billing/offers` | Return active, server-approved offer snapshots |
| `GET /api/billing/summary` | Return the user's normalized billing and entitlement state |
| `POST /api/billing/checkouts` | Create a provider-hosted checkout/mandate flow |
| `POST /api/billing/subscriptions/:id/cancel` | Cancel future renewals; retain the already-paid access window |
| `POST /api/billing/webhooks/cashfree` | Receive signed Cashfree events; no user auth |

The frontend should learn access through `/api/billing/summary`; it must not
infer paid status from query parameters, local storage, or a provider response.

## Checkout and webhook sequence

```mermaid
sequenceDiagram
    participant U as User browser
    participant K as Katalume API
    participant P as Payment provider

    U->>K: Create checkout (offerKey + idempotency key)
    K->>K: Validate active offer and snapshot terms
    K->>P: Create hosted checkout/mandate
    P-->>K: Provider checkout reference
    K-->>U: Hosted checkout URL/reference
    U->>P: Complete provider-controlled payment flow
    P-->>U: Return to informational status page
    P->>K: Signed webhook
    K->>K: Verify raw-body signature and acquire event once
    K->>K: Validate amount/currency and update billing state
    K->>K: Create or revoke entitlement grant
    K-->>P: 2xx after processing
    U->>K: Fetch billing summary
    K-->>U: Verified entitlement
```

Webhook processing rules:

1. Read the raw request body with a strict size limit.
2. Select the secret by provider and environment; verify before JSON-driven
   side effects.
3. Persist the unique event and payload hash before acknowledging it.
4. Return success for an identical replay.
5. Acquire the event with a compare-and-set processing lease so concurrent
   deliveries do not execute twice; a stale lease may be retried.
6. Reject stale subscription-state transitions using provider occurrence time.
7. Validate the server-owned amount and currency before every grant.
8. Scheduled and admin-triggered reconciliation compare internal
   source/grant/receipt invariants and provider resource state, then persist
   alerts. Reconciliation is detect-only: it never repairs entitlements,
   issues refunds, or mutates Cashfree.

## India-first payment requirements

The initial experience should:

- use INR and integer paise;
- prefer a provider-hosted checkout to minimize payment-data scope;
- support UPI AutoPay where the chosen provider and merchant account permit it,
  with cards/e-mandate as evaluated fallbacks;
- explain mandate frequency, amount, start/end, cancellation, and any trial
  before authorization;
- expect customer pause/revoke/cancel actions outside Katalume and consume the
  corresponding webhook;
- show renewal date, final payable amount, tax treatment, invoice access, and
  cancellation behavior in plain language;
- support pre-debit notifications and provider-required additional
  authentication without representing a pending debit as paid.

NPCI documents customer controls including modify, revoke, pause, and unpause,
as well as pre-debit notification at least 24 hours before execution for the
covered AutoPay flow. Provider capabilities and thresholds change, so verify
them during activation rather than hard-coding them.

Before accepting money, the owner must obtain professional decisions on GST
registration, place of supply, invoice fields/numbering, tax-inclusive display,
refund/cancellation terms, consumer disclosures, accounting, and e-invoicing
applicability. Store the tax decision and invoice snapshot used for each
transaction; do not recompute old invoices from current settings.

## Security and privacy controls

- Separate live and test provider accounts, IDs, webhook URLs, and secrets.
- Keep secrets only in the backend secret manager; never use `NEXT_PUBLIC_*`.
- Rotate webhook/API secrets and support overlapping verification during
  rotation.
- Restrict billing admin actions with MFA, least privilege, reason capture, and
  immutable audit events.
- Use constant-time signature comparison where the provider library does not.
- Enforce server-side offer lookup; reject client-supplied amounts, currency,
  tier, tax, or provider plan IDs.
- Bind checkout ownership to the authenticated Katalume user.
- Cancel renewable mandates before account deletion; anonymize billing contact
  data retained for financial/legal records and remove product entitlements.
- Tokenize through the provider and keep Katalume out of raw card/UPI data.
- Redact provider payloads, email, phone, GSTIN, addresses, and failure detail
  from ordinary logs and error reporting.
- Rate-limit checkout creation, status polling, cancellation, refunds, and
  webhook failures independently.
- Reconcile captured payments with settlements and alert on money/access
  disagreement.

## Feature flags and configuration

The safe default in every environment is off:

```text
BILLING_ENABLED=false
CHECKOUT_ENABLED=false
BILLING_PROVIDER=disabled
BILLING_WEBHOOK_PROCESSING_ENABLED=false
BILLING_RECONCILIATION_ENABLED=false
PAID_ENTITLEMENTS_ENFORCED=false
```

Activation must be staged. Enabling checkout must not automatically enforce
paid entitlements, and enabling entitlement enforcement must not expose
checkout. Unknown or missing configuration fails closed for new purchases while
preserving already-verified access.

Cashfree-specific configuration:

```text
CASHFREE_CLIENT_ID
CASHFREE_CLIENT_SECRET
BILLING_WEBHOOK_URL
BILLING_ENVIRONMENT=sandbox|production
BILLING_RECONCILIATION_INTERVAL_MINUTES
BILLING_RECONCILIATION_BATCH_SIZE
```

Secrets belong only in Render's secret store. `BILLING_WEBHOOK_URL` must be the
direct public backend URL ending in `/api/billing/webhooks/cashfree`; do not
route it through the frontend BFF because signature verification uses the exact
raw request bytes and Cashfree headers.

## Testing strategy

### Unit and contract tests

- money/currency parsing and immutable offer snapshots;
- every normalized state transition, including invalid and out-of-order events;
- entitlement overlap, expiry, refund, cancellation, grace, and lifetime cases;
- provider adapter contract against captured, sanitized fixtures;
- signature verification, tampered payloads, duplicate IDs, and conflicting
  replays;
- authorization and ownership checks for every route.

### Integration and failure tests

- checkout request retry with one idempotency key;
- duplicate/out-of-order webhook delivery;
- event acquired but the API interrupted before state update;
- provider timeout after an unknown checkout result;
- successful payment with delayed webhook;
- failed renewal, recovery, cancellation, refund, chargeback, and dispute;
- database failure at each write boundary and stale processing-lease recovery;
- secret rotation and test/live environment isolation.

### Production-like proof

Use provider test mode plus a dedicated staging deployment. Rehearse
reconciliation, rollback, support lookup, invoice generation, data
export/deletion, and webhook replay. Run a low-value owner-only live transaction
and refund only after legal, tax, and launch approval.

## Observability and operations

Measure checkout creation/error rate, webhook verification failures, processing
lag, dead letters, payment success by method, renewal recovery, entitlement
update latency, refunds/disputes, and reconciliation drift. Never use raw
payment identifiers or personal data as metric labels.

Alerts should cover:

- any captured payment without a matching entitlement after the target window;
- any entitlement with a refunded/reversed source;
- rising signature failures or webhook backlog;
- test events/secrets reaching production or the reverse;
- settlement mismatch and repeated reconciliation failure.

Provide runbooks for provider outage, webhook outage, payment-without-access,
access-without-payment, cancellation, refund, dispute, account merge, secret
rotation, and provider migration.

## Provider migration

Because Katalume owns offers and entitlements, a migration can:

1. stop new checkouts on provider A;
2. keep consuming A's renewals and webhooks;
3. create new customers/subscriptions on provider B;
4. map both providers into the same normalized ledger;
5. invite existing users to authorize a new mandate where portability is not
   available;
6. retire A only after refunds, disputes, reconciliation, and retention close.

Never overwrite provider A identifiers with provider B identifiers.

## Activation gates

- [x] Free-versus-paid benefits implemented behind independent enforcement
- [ ] Weekly/monthly/yearly/lifetime prices and tax display approved for live sale
- [ ] Lifetime terms and business liability approved
- [x] Cashfree selected as the initial India-first adapter
- [ ] Legal entity, bank/settlement, KYC, GST, invoices, terms, privacy, refunds, and cancellation approved
- [x] Backend models, adapter, APIs, access checks, and indexes implemented
- [x] Frontend pricing, hosted checkout, membership, cancellation, and recovery UX implemented
- [x] Webhook signature, replay, amount verification, subscription, and lifetime tests green
- [x] Payment receipt ledger, customer receipt history, internal/provider reconciliation, persistent alerts, and audited Admin billing console implemented
- [x] Draft Terms, Privacy, Refund, Billing Terms, About, and Contact pages linked from checkout
- [ ] Test/live secrets and environments isolated
- [ ] Support and finance runbooks rehearsed
- [ ] Accessibility, mobile, localization, analytics-consent, and security review passed in staging
- [ ] Staged test-mode and owner-only live canary passed
- [ ] `BILLING_ENABLED` separately approved for production

Until every applicable gate is closed, Katalume remains free and all billing
flags remain false.

## Current references

- [NPCI: UPI AutoPay](https://www.npci.org.in/product/autopay)
- [Reserve Bank of India: e-mandate framework index/circular](https://www.rbi.org.in/scripts/bs_circularindexdisplay.aspx/Scripts/BS_CircularIndexDisplay.aspx?Id=12722)
- [Cashfree: subscription FAQ](https://www.cashfree.com/docs/payments/subscription/faq)
- [Cashfree: webhook signatures](https://www.cashfree.com/docs/payments/subscription/webhook-signature)
- [Cashfree: subscription API](https://www.cashfree.com/docs/api-reference/payments/latest/subscription/overview)
- [Cashfree: hosted web checkout](https://www.cashfree.com/docs/payments/online/web/redirect)
- [GST portal: e-invoicing glossary](https://tutorial.gst.gov.in/downloads/news/pamphlet_e_invoicing_glossary_updated_17_08_2023_approved_final.pdf)

These links are implementation inputs, not permanent constants. Re-check them
and the chosen provider's current merchant capabilities immediately before
activation.
