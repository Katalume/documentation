# Production readiness

**Assessment:** 2026-07-13
**Decision:** **NO-GO for unrestricted production traffic; code-side beta architecture ready for review**

## P0 gate register

| Gate | Current evidence | State |
|---|---|---:|
| Private source | All six repos private; anonymous API returns 404 | Closed |
| Browser/API boundary | Same-origin BFF; no production bearer response/storage | Closed |
| Session security | Tracked hashes, rotation, reuse detection, revocation checks | Closed |
| Run/submit contract | `202`, job/submission polling, live contract tests | Closed |
| Durable execution | Atomic Mongo claim, heartbeat, retry/backoff/dead-letter | Closed |
| Judge safety | Auth, async poll, timeout/concurrency, CPU/memory/wall/file bounds | Closed in code |
| Verdict safety | Full terminal enum and safe exhaustion finalization | Closed |
| Contest correctness | Window/registration/problem attribution and scoring | Closed for beta rules |
| Runtime/dependencies | Node 24; zero production audit findings; non-root images | Closed |
| Abuse/input | Redis distributed limits, per-user quota, unsafe-key rejection | Closed in code |
| Data/content | Migration, indexes, pagination, version-switched testcases | Closed in code |
| CI/security | PR 22/31 Actions gates, audits, images, Gitleaks, Dependabot | Closed in code; Vercel plan gate open |
| Branch/release protection | GitHub rejects protection for private org repos on current plan | Owner/platform gate |
| Production infrastructure | Managed Mongo/Redis/API/worker/private Judge0 | Open |
| Production configuration | HTTPS URLs, live/no fallback, secrets, Sentry, private Vercel plan | Open |
| Identity lifecycle | Email verification/recovery and Admin MFA | Open |
| Observability | Central metrics/traces/errors/alerts and on-call routing | Open |
| Recovery/capacity | Restore, rollback, resilience and 2× peak load drills | Open |
| Credentials | Confirm legacy Judge0 values rotated everywhere | Open |

## Completed P1 engineering

- Admin mutation audit stream; cascading user deletion/account export
- Request correlation and structured logs; Mongo/Redis readiness
- Editorial server-side unlock enforcement
- Problem/user/submission pagination and compound indexes
- Atomic testcase content versioning and idempotent versioned seeds
- JSON stdin/stdout problem execution contract with public/hidden cases
- Contest registration race protection and unique leaderboard rows
- CSP, HSTS, canonical/social metadata, robots and sitemap

## Remaining owner/platform work

- Select/provision domains, cloud services, secrets, mail/identity, monitoring,
  support/status and backup retention.
- Upgrade the organization plan (or move to an equivalent private host) so
  protected branches and private-org Vercel deployments are available.
- Approve legal/privacy/acceptable-use/retention terms, content/dataset rights,
  trademark clearance, analytics consent and commercial policy.
- Establish expected beta peak, SLOs/error budgets, cost ceilings and on-call.
- Run manual accessibility, real Judge0, performance, recovery and incident drills.

## Verified evidence

- Frontend: lint/typecheck/build, 26 unit/component tests, 8 Playwright journeys
- Backend: 20 suites / 115 tests; 83.13% statements, 68.32% branches,
  80.71% functions and 85.29% lines with enforced thresholds
- Frontend/backend production dependency audits: zero known findings
- Both Node 24 images build and run as non-root
- Isolated production-mode container smoke returned ready Mongo/Redis, enforced
  cross-origin denial, issued only HttpOnly cookies, and finalized a durable job
- Gitleaks: frontend clean; backend clean after two exact documented legacy
  fingerprints, with no broad rule/path exclusions
- Documentation strict build green
- Backend PR 22 and frontend PR 31 contain the reviewed hardening branches;
  GitHub Actions code gates are green. Vercel's private-org Hobby-plan status is
  an explicit platform failure, not an application test failure.

## Go/no-go rule

Do not promote production until every open P0 row has an owner and evidence;
application CI and protected release rules are green; real private Judge0 and
production-like staging pass; backup/rollback and alerts are demonstrated; no
high/critical finding is accepted silently; and a signed go/no-go review is
recorded.
