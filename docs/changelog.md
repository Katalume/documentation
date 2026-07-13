# Changelog

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
