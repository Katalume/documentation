# July 20 launch plan

**Window:** July 13–20, 2026  
**Recommended launch type:** controlled public beta, not unrestricted GA

Seven days is enough only with scope discipline, parallel ownership, and hard
go/no-go gates. Do not hide incomplete infrastructure behind the finished UI.

## Scope decision by July 13

For the July 20 beta:

- Support Python/Judge0 language `71` only.
- Keep strict execution quotas.
- Disable competitions unless attribution/scoring clears staging by July 18.
- Restrict Admin access to named accounts with MFA or equivalent control.
- Launch core journey: signup/login → browse → run → submit → history/progress.
- Do not enable billing.

## Daily critical path

### July 13 — freeze, assign, protect

- Name accountable owner for frontend, API, execution, data, security, and launch.
- Freeze UI except P0 correctness/accessibility regressions.
- Protect GitHub branches and required checks.
- Move CI/Docker to Node 24 LTS.
- Patch frontend high dependency findings.
- Finalize domains, hosting, managed Mongo, Redis, Judge0 host, Sentry, email.
- Rotate all credentials and inventory secrets.

**Exit:** owners and environments exist; no ambiguity about beta scope.

### July 14 — staging foundation

- Deploy backend staging with managed MongoDB/TLS/backups.
- Deploy private authenticated Judge0 on isolated host.
- Deploy queue and worker skeleton with durable job IDs.
- Configure staging frontend live mode with fallback false.
- Align CORS/cookies/domains and verify signed session navigation.

**Exit:** staging health/readiness green; no mock data in live mode.

### July 15 — live contract and execution

- Fix `/runner/run` frontend contract and language propagation.
- Implement `202` submission flow, polling/status, worker finalization.
- Forward resource limits and add HTTP timeouts.
- Map every Judge0 verdict and reconcile stuck Pending jobs.
- Add idempotency/retry/dead-letter behavior.

**Exit:** compile, accepted, wrong, runtime, timeout, memory, and infrastructure
failures all produce correct durable user-visible results.

### July 16 — authentication and data safety

- Move bearer token out of persistent localStorage or ship BFF design.
- Implement rotated/revocable refresh sessions and logout invalidation.
- Implement email verification and password recovery/change.
- Add Admin MFA/short privileged sessions.
- Add critical indexes, migrations, retention decisions, and safe cascades.

**Exit:** account takeover/session and data-integrity review signed off.

### July 17 — test and load

- Run real staging E2E for learner and Admin critical paths.
- Run API/queue/Judge0 load test at 2× expected beta peak.
- Run dependency/SAST/secret/container scans.
- Run manual keyboard/screen-reader/mobile/slow-network pass.
- Run backup restore drill and worker/API rollback drill.

**Exit:** capacity, restore, rollback, and accessibility evidence recorded.

### July 18 — operations and product truth

- Enable structured logs, Sentry, metrics, traces, SLO dashboards, alerts.
- Test every runbook with the responsible owner.
- Complete content QA for launch problems/tracks/hidden tests/editorials.
- Decide competition gate based on real scoring evidence; disable if not green.
- Complete terms, privacy, acceptable-use, support, and status page.

**Exit:** operators can detect, diagnose, communicate, and recover failures.

### July 19 — release candidate

- Cut immutable release candidate; freeze features.
- Promote to production with traffic restricted to internal/invite cohort.
- Run production smoke journey without mock fallback.
- Observe for at least one representative traffic window.
- Resolve only release blockers; rehearse rollback and launch communication.

**Exit:** signed go/no-go packet with all P0 evidence.

### July 20 — staged beta

1. Final 30-minute go/no-go review.
2. Open traffic in stages (for example 5% → 25% → 50% → 100% beta cohort).
3. Hold at each stage while checking auth, API, Mongo, queue, Judge0, error,
   latency, and cost dashboards.
4. Stop/roll back on error-budget, queue-age, security, or data-integrity breach.
5. Publish known limitations and support/status channels.

## Automatic no-go conditions

- Any P0 gate lacks evidence or owner.
- Frontend can fall back to mock in production.
- Real Run/Submit/history journey fails.
- Queue jobs can disappear or remain Pending without reconciliation.
- Judge0 is public/unauthed or shares a host with API/data.
- High/critical dependency finding remains unapproved.
- Backup restoration or rollback has not been demonstrated.
- Monitoring cannot detect API/Judge/queue/database failure.
- Terms/privacy/acceptable-use and support channel are missing.

## If schedule slips

Prefer a smaller honest beta over a cosmetically complete but unsafe launch.
Disable competitions, reduce languages, cap cohort size/executions, and keep
Admin/content writes restricted. Never launch mock fallback as real product
behavior.
