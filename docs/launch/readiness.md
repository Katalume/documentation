# Production readiness

**Assessment date:** 2026-07-13  
**Decision:** **NO-GO for unrestricted public launch today**

The user experience, visual identity, core CRUD, and deterministic test suites
are strong. The deployed frontend is nevertheless in mock mode, there is no
recorded backend production deployment, and live execution/auth/contest
architecture has material launch blockers.

## P0 launch gates

Every row must be closed before real public traffic.

| Gate | Current evidence | Exit criterion |
|---|---|---|
| Backend production stack | No deployment recorded | Staging + production API, managed Mongo, private queue/Judge0, rollback tested |
| Frontend live configuration | Vercel has zero production env vars | `live`, fallback false, production API URL/Sentry/domain verified |
| Run contract | Frontend `/submissions/run`; backend `/runner/run` | One schema, live contract test green |
| Async judging | `wait=true`, sequential, no Axios timeout | Durable queue/workers, 202/status, idempotency, retry/DLQ/reconciliation |
| Resource enforcement | Test limits built then dropped | CPU/memory/wall/output limits reach Judge0 and are tested |
| Verdict safety | Submission enum incomplete | Every Judge0 status maps/persists without stuck Pending |
| Session security | Bearer/session in localStorage; refresh not revocable | Memory/BFF access, rotated tracked refresh sessions, revocation/reuse detection |
| Account lifecycle | No verification/reset/MFA | Email verification, password recovery/change, Admin MFA, deletion/export |
| Runtime security | CI/Docker Node 20 EOL | Pinned supported Node 24 LTS across CI/deploy |
| Frontend dependencies | 2 high, 12 moderate, 2 low audit findings | No high/critical; reviewed remediation for remaining findings |
| Live test evidence | E2E is mock-only; Judge mocked | Full staging journey with real API/Mongo/queue/Judge0 |
| Source/release protection | GitHub branches unprotected | Required checks/reviews, no force push, protected production promotion |
| Credentials | Development exposure history exists | All credentials rotated; secret scan and inventory clean |

## P1 general-availability work

### Contest correctness

- Associate submissions with contest and enforce registration/time windows.
- Update standings from accepted submissions atomically.
- Add unique contest/user rows, scoring/penalties/tie-breaks/freeze rules.
- Prevent registration after end and race duplicates.

### Data integrity and performance

- Add compound indexes and pagination.
- Add migrations and transaction/compensation boundaries.
- Resolve orphan behavior for problem, contest, and user deletion.
- Optimize progress/profile aggregation rather than loading all records.
- Define submission/source/log retention and account erasure.

### API and content correctness

- Validate every route body/query/ID with a consistent schema system.
- Make problem/testcase authoring atomic.
- Enforce editorial unlock rules.
- Reject malformed auth responses instead of inventing access tokens.
- Standardize error codes/request IDs and safe response bodies.

### Scale, abuse, and operations

- Configure trusted proxy and distributed Redis rate limits.
- Add per-user/IP execution quotas, bot protection, cost ceilings.
- Load-test API/Mongo/queue/Judge0 at launch concurrency.
- Add structured logs, metrics, traces, alerts, readiness, and runbooks.
- Demonstrate backup restoration and rollback.

### Quality

- Frontend coverage report/thresholds and live contract tests.
- Raise backend branch coverage above 67.27%; test runner/Judge directly.
- Add security, performance, accessibility, visual, concurrency, migration,
  container, and resilience gates.

## P2 market readiness

- Custom domain, DNS/TLS, CSP and other browser security headers
- Canonical metadata, Open Graph, robots, sitemap, structured data
- Terms, privacy, cookie/analytics disclosure, acceptable-use/execution policy
- Trademark clearance/registration and protected handles/domains
- Launch-quality catalog/editorials/hidden tests and dataset rights
- Support/contact/status page, onboarding and transactional email
- Moderation, abuse appeals, admin audit history
- Pricing/billing/tax/refund systems if monetized
- Consent-aware analytics and product/cost dashboards
- Manual WCAG 2.2 AA and Core Web Vitals budgets

## Evidence baseline

- Frontend: lint/typecheck/build green, 21 unit tests, 8 Playwright tests
- Backend: 91 tests; 84.3% statement / 67.27% branch coverage
- Backend production dependency audit: zero known vulnerabilities
- Frontend production dependency audit: 16 findings, including two high
- Vercel production: Ready but mock-configured
- Frontend `develop`: `ccd6503`; `main`: `71cf79b`
- Backend `main`: `d144514`

## Go/no-go rule

Launch only when every P0 gate has an owner, evidence link, and signed closure;
relevant P1 systems have passed staging; no high/critical dependency issue is
accepted without explicit risk approval; restore/rollback work; and the full
production-like journey succeeds without mock fallback.
