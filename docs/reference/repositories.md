# Repository reference

## Active repositories

### `MLBoost2025/frontend`

- Next.js/React product
- Default branch: `develop`
- Release mirror/production branch: `main`
- Latest documented `develop`: `ccd6503`
- Latest documented `main`: `71cf79b`
- PR #30 merged the Ensemble Spark identity into `develop`

Key paths:

- `src/app` — routes and UI
- `src/app/components` — shared product components
- `src/lib/api.ts` — live/mock API boundary
- `src/context/AuthContext.tsx` — session state
- `src/proxy.ts` — route/session validation
- `e2e` — Playwright flows

### `MLBoost2025/backend-api`

- Express/Mongoose/Judge0 API
- Default branch: `main`
- Latest documented main: `d144514`

Key paths:

- `src/routes` — endpoint map
- `src/controllers` — request/domain orchestration
- `src/models` — Mongo schemas
- `src/services/judge.service.js` — Judge0 integration
- `src/workers` — placeholder async execution files
- `tests` — Jest/Supertest integration tests
- `judge0` — vendored Judge0 CE configuration

### `MLBoost2025/documentation`

- Product, engineering, API, operations, and launch source of truth
- Default branch: `main`
- Built with MkDocs Material

## Inactive or cleanup candidates

- `backend-api/client` — unused earlier Vite frontend
- duplicated backend `auth`, `problem`, and `contest` service modules not used by
  active controllers
- placeholder Redis/queue/worker files to replace with the real async design
- empty organization repositories unless ownership/scope is explicitly assigned

## Branch strategy recommendation

- Backend: protected `main`, short-lived branches, PRs, immutable releases.
- Frontend: decide whether `develop` adds value. If retained, document promotion
  `feature → develop → release/main`; otherwise use protected trunk `main`.
- Documentation: protected `main`; direct push was used only for the initial
  owner-authorized bootstrap.

No default branch should accept force pushes or bypass required checks for normal
delivery.
