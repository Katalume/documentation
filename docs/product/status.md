# Current product status

**Snapshot:** 2026-07-13

## Implemented and verified locally

- Approved Blue Eclipse/Ensemble Spark product UI retained
- Same-origin Next.js BFF with production cookie-only sessions
- Rotating/revocable Session records and account lifecycle operations
- Durable Mongo evaluation jobs, separate worker, retry/dead-letter/heartbeat
- Async Judge0 polling with resource limits and bounded concurrency
- Idempotent submission/status/cancellation contract
- Contest eligibility, attribution and exactly-once first-solve scoring
- Atomic-version testcase authoring and JSON program I/O contract
- Distributed Redis throttling, per-user execution quota, input guard
- Request IDs, structured logs, Admin audit, liveness/readiness
- Node 24 non-root frontend/backend images and zero production audit findings
- CSP/HSTS/privacy headers, metadata, robots and sitemap
- Full-history secret scanning and public visibility for all six repositories
- Container smoke: BFF → API → Mongo/Redis → worker → Judge simulator → verdict
- Frontend PRs 31/32 and backend PR 22 merged with green Actions and deployment checks

## Hosted state still requiring deployment work

- Existing Vercel production alias predates this hardening and lacks required
  production configuration.
- The merged frontend was preview-deployed successfully after repository
  visibility changed; production still needs explicit environment configuration.
- Required branch rules and release approvals still need owner configuration.
- No managed production backend/worker/Judge0 deployment is recorded.
- No custom domain, centralized monitoring evidence, backup restore, load test,
  or rollback drill is recorded.
- Email verification/recovery and mandatory Admin MFA are not complete.
- Two exact passwords existed in removed legacy Judge0 history; scanning now
  pins only those fingerprints, but any environment that used them must rotate.

## Readiness

Application architecture is beta-capable; production infrastructure and owner
controls are not yet proven. The authoritative decision remains
[Production readiness](../launch/readiness.md).
