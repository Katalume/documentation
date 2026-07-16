# Changelog

## 2026-07-16

- Published the zero-cost beta at `katalume.vercel.app` with a live same-origin
  API on Render, Atlas data, and Upstash distributed rate limits.
- Added a versioned 126-problem catalog: 42 Easy, 42 Medium, and 42 Hard, backed
  by 3,486 deterministic testcases (8/25/50 by difficulty).
- Added pinned local CPython/Pyodide execution in a disposable worker, bundled
  Monaco/runtime assets, local practice progress/history, and explicit browser
  result attribution.
- Kept server execution and ranked contests fail-closed while preserving the
  isolated Judge0 worker path for a later paid upgrade.
- Merged backend PR 28 and frontend PRs 40/41 with green tests, E2E, audit,
  secret scan, and deployment checks.
- Configured live Vercel production variables, narrow Render-to-Atlas network
  access, corrected Upstash TCP TLS credentials, and verified public health,
  readiness, BFF, catalog, and security-header contracts.
- Updated Google OAuth to the Vercel callback and moved the external app to
  production.
- Configured GitHub OAuth in Render, deployed the provider without exposing its
  credentials, and verified the live GitHub authorization and Vercel callback
  flow.
- Activated the `ml-problems` content pipeline with secret-isolated GitHub
  synchronization and imported 72 additional problems / 1,353 testcases
  without collisions or unintended archives.
- Expanded production to 198 problems and 4,839 active testcases.
- Added live, versioned browser-practice suite delivery so repository-imported
  problems execute without rebuilding the frontend catalog.
- Production browser verification passed imported Easy, Medium, and Hard
  submissions at 9/9, 22/22, and 40/40; disposable accounts were deleted.
- Completed a real public Chrome smoke (2/2 samples and 8/8 practice tests) and
  deleted the disposable account and credentials afterward.

## 2026-07-13

- Implemented durable asynchronous evaluation, tracked rotating Sessions,
  same-origin BFF authentication, contest scoring, testcase versioning,
  distributed quotas, request/audit observability, migrations and account lifecycle.
- Pinned Node 24, built non-root production images, reduced production audits to
  zero findings, added full-history secret scanning and Dependabot.
- Container-verified BFF/API/Mongo/Redis/worker/Judge lifecycle and privacy headers.
- Made all six organization repositories public while keeping repository links
  out of the product website.
- Merged frontend PRs 31/32 and backend PR 22 with green application,
  deployment, container, and secret-scan gates.
- Rebranded the product as Katalume across code, configuration, metadata,
  tests, documentation, and the visual identity assets.

- Bootstrapped the complete documentation repository.
- Documented product scope, user journeys, current state, and brand system.
- Documented current and target architecture, trust boundaries, data models, and
  architecture decisions.
- Added user, Admin, local development, and testing guides.
- Added complete current API reference and authentication model.
- Added environment, deployment, observability, security, and incident runbooks.
- Added evidence-backed production-readiness assessment and July 20 launch plan.
- Added configuration/repository/glossary references, contribution rules,
  security policy, strict MkDocs build, and Documentation CI.

## Product delivery captured

### Frontend

- Public landing and authenticated dashboard
- Premium responsive/a11y UI and solve arena
- Blue Eclipse visual system and Space Mono/Plus Jakarta Sans typography
- Ensemble Spark identity
- Live/mock data boundary, signed session proxy, Admin CRUD, contests, tracks,
  profile, progress, critical unit and browser coverage

### Backend

- Auth/session hardening, ownership and role controls
- Problems/testcases/submissions, progress/profile/leaderboards
- Contest details/registration/leaderboard reads
- Learning tracks and Admin CRUD
- Async Judge0 integration, execution guards, durable jobs, Redis limits,
  production containers, enforced coverage, and more than 110 tests
