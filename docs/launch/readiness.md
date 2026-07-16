# Production readiness

**Assessment:** 2026-07-16
**Decision:** **GO for a clearly labelled, zero-cost public practice beta; NO-GO for ranked or SLO-backed server execution**

## Launch gate register

| Gate | Current evidence | State |
|---|---|---:|
| Public frontend | Vercel production alias is `Ready` | Closed |
| Public API | Render latest `f99bde8` deploy is `live` | Closed |
| Browser/API boundary | Same-origin BFF; production bearer responses disabled | Closed |
| Session security | Tracked rotation/revocation and HttpOnly Katalume cookies | Closed |
| Authentication | Email/password plus external Google and GitHub OAuth in production | Closed for beta |
| Catalog | 126 problems: 42 Easy, 42 Medium, 42 Hard | Closed |
| Test content | 3,486 deterministic cases: 8/25/50 per Easy/Medium/Hard problem | Closed |
| Practice execution | Local pinned CPython/WASM worker; real public 8/8 acceptance | Closed |
| Server execution | Fail-closed with `EXECUTION_MODE=disabled` | Closed by restriction |
| Data/cache | Atlas and Upstash readiness true | Closed for beta |
| Network access | Only current operator IP and exact Render outbound CIDRs | Closed |
| CI/security | Frontend #40/#41 and backend #28 green and merged | Closed |
| Production configuration | Live/no-fallback Vercel env and Render secrets verified | Closed |
| Branch/release protection | Review bypass remains possible for owner | Owner gate |
| Identity lifecycle | Email verification/recovery and Admin MFA | Open |
| Observability | Central metrics/errors/alerts and on-call routing | Open |
| Recovery/capacity | Restore, rollback, resilience, and peak-load drills | Open |
| Ranked contests | Requires durable isolated server judge | Disabled |

## Free-beta operating rules

1. Market the current release as a public practice beta, not a guaranteed judge.
2. Keep ranked contest registration and server run/submit disabled.
3. Never keep Render awake with artificial traffic or weaken Atlas to
   `0.0.0.0/0`.
4. Treat local browser history as device-local; do not promise cross-device
   progress until durable server submissions are enabled.
5. Monitor Atlas, Upstash, Render, and Vercel quotas weekly.

## Remaining owner/platform work

- Configure protected branches and independent release approvals.
- Approve privacy, acceptable-use, retention, content-rights, trademark,
  analytics-consent, and commercial terms.
- Add central error reporting and an owner-tested incident/rollback path.
- Establish expected beta peak, SLOs/error budgets, and free-tier exhaustion
  behavior.

## Upgrade gate for server Submit

Do not enable `EXECUTION_MODE=judge0` until private Judge0 and its Redis/Postgres
stores are isolated from application data; workers, retries, dead letters,
resource/network limits, alerting, load, restore, and rollback have passed in a
production-like environment. Browser Run can remain available during and after
that migration.
