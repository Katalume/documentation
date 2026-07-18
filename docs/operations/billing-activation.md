# Billing activation runbook

The code and UI are deployable with all billing flags disabled. Do not combine
deployment with commercial activation.

## Safe deployed state

```text
BILLING_ENABLED=false
CHECKOUT_ENABLED=false
BILLING_WEBHOOK_PROCESSING_ENABLED=false
PAID_ENTITLEMENTS_ENFORCED=false
BILLING_PROVIDER=disabled
BILLING_ENVIRONMENT=sandbox
```

In this state the membership catalog may be visible, no Cashfree request is
made, and all problems remain accessible.

## Owner prerequisites

1. Complete Cashfree merchant, KYC, bank and settlement onboarding.
2. Obtain professional decisions for GST, place of supply, invoices, refunds,
   cancellation wording, consumer terms, privacy and lifetime liability.
3. Approve version 1 prices and tax-inclusive or tax-exclusive presentation.
4. Publish Terms, Privacy, Refund and Cancellation pages linked from checkout.
5. Create separate Cashfree sandbox and production credentials.

Payment processing is not free: provider transaction and applicable tax fees
must be included in unit economics even though the disabled implementation has
no fixed billing infrastructure charge.

## Sandbox activation

Set backend secrets without printing them:

```text
BILLING_PROVIDER=cashfree
BILLING_ENVIRONMENT=sandbox
CASHFREE_CLIENT_ID=<sandbox id>
CASHFREE_CLIENT_SECRET=<sandbox secret>
BILLING_WEBHOOK_URL=https://katalume-api.onrender.com/api/billing/webhooks/cashfree
```

Enable in this order, with a redeploy and smoke test after each step:

1. `BILLING_ENABLED=true`
2. `BILLING_WEBHOOK_PROCESSING_ENABLED=true`
3. `CHECKOUT_ENABLED=true`
4. Keep `PAID_ENTITLEMENTS_ENFORCED=false`.

Test all four offers. Verify:

- a checkout retry with the same idempotency key creates one provider resource;
- a browser return does not change access;
- an invalid signature, stale timestamp, wrong amount and wrong currency grant
  nothing;
- a valid recurring `CHARGE` creates a bounded Plus grant;
- a valid Lumus payment creates one non-expiring grant;
- a verified full Lumus refund revokes that grant;
- duplicate webhooks produce one event and one grant;
- cancellation stops renewal but preserves paid access through period end;
- account deletion first cancels any renewable mandate, then anonymizes retained
  billing records and removes product entitlements;
- no payment credentials or unredacted provider payload appear in logs.

## Staging enforcement

After sandbox payment and cancellation pass, set
`PAID_ENTITLEMENTS_ENFORCED=true` in staging only. Verify:

- the problem list labels free and Plus problems;
- all 60 free problems open and run;
- a free user cannot fetch a paid problem body or its practice testcases;
- Plus and Lumus users can open all problems;
- Interview Tracks is blocked for Free and remains Coming Soon for paid users;
- Learn and Competitions remain unchanged;
- expired grants return to Free without losing history.

## Production canary

1. Switch to isolated production Cashfree keys and
   `BILLING_ENVIRONMENT=production`.
2. Keep paid enforcement off.
3. Enable billing, webhook processing and checkout.
4. Run one low-value owner-only live recurring purchase and one Lumus purchase.
5. Verify settlement, entitlement, cancellation and refund end to end.
6. Revoke or rotate test credentials and confirm sandbox events cannot reach
   production.
7. Enable paid enforcement only after the owner signs off and support is ready.

## Rollback

Disable in this order:

1. `CHECKOUT_ENABLED=false`
2. `PAID_ENTITLEMENTS_ENFORCED=false`
3. Keep webhook processing on while active subscriptions can still produce
   money events.

Do not disable webhook processing merely because checkout is off. Existing
mandates may renew until cancelled. If the provider is unhealthy, stop new
checkout first and preserve the last verified, unexpired entitlements.
