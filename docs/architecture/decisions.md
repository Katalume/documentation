# Architecture decisions

This page records the decisions already embodied in the code and the decisions
required for launch.

## Accepted

### ADR-001 — Next.js App Router frontend

Use Next.js for public marketing, authenticated product routes, proxy-based
navigation checks, Vercel deployment, and route-level loading/error boundaries.

### ADR-002 — One frontend data boundary

All page data access goes through `src/lib/api.ts`. This prevents components from
inventing their own transport/auth behavior. The module should now be split
internally while preserving one public boundary.

### ADR-003 — MongoDB domain persistence

Use Mongoose models for users, content, submissions, competitions, tracks, and
leaderboards. Add migrations, indexes, retention, and transaction boundaries for
production.

### ADR-004 — Judge0 execution boundary

Do not execute untrusted code in the public API process. Judge0 remains a
separate, private, privileged execution subsystem.

### ADR-005 — Blue Eclipse and Ensemble Spark

Blue Eclipse is the canonical product palette; Plus Jakarta Sans and Space Mono
are the type system. Ensemble Spark is the canonical mark.

## Required before launch

### ADR-006 — Durable async execution

Adopt Redis-backed durable jobs with API/worker separation, idempotency,
bounded retries, dead-letter handling, and reconciliation.

### ADR-007 — Revocable sessions

Use short-lived access tokens and rotated, hashed, server-tracked refresh
sessions. Do not store live bearer tokens persistently in browser storage.

### ADR-008 — Controlled launch scope

If the July 20 deadline is immovable, ship a controlled beta with a single
language, strict execution quotas, and competitions disabled until real scoring
and contest attribution pass staging tests.

### ADR-009 — Environment promotion

Promote the same immutable release from staging to production. Production
requires explicit approval, post-deploy smoke tests, observable health, and a
tested rollback.
