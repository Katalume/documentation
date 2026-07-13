# Repository reference

## Active repositories

### `Katalume/frontend`

- Next.js/React product
- Default branch: `develop`
- Release mirror/production branch: `main`
- Public repository; hardening PR 31 is merged to `develop` and sync PR 32 is merged to `main`

Key paths:

- `src/app` — routes and UI
- `src/app/components` — shared product components
- `src/lib/api.ts` — live/mock API boundary
- `src/app/api/[...path]` — same-origin BFF
- `src/context/AuthContext.tsx` — session state
- `src/proxy.ts` — route/session validation
- `e2e` — Playwright flows

### `Katalume/backend-api`

- Express/Mongoose/Judge0 API
- Default branch: `main`
- Public repository; hardening PR 22 is merged to `main`

Key paths:

- `src/routes` — endpoint map
- `src/controllers` — request/domain orchestration
- `src/models` — Mongo schemas
- `src/services/judge.service.js` — Judge0 integration
- `src/services/evaluation.service.js` — durable queue/job lifecycle
- `src/workers` — evaluation worker entrypoint
- `tests` — Jest/Supertest integration tests

### `Katalume/documentation`

- Product, engineering, API, operations, and launch source of truth
- Default branch: `main`
- Built with MkDocs Material

All six organization repositories are public. `evaluation-engine` and
`ml-problems` remain reserved/empty; active evaluation/content implementations
currently live in `backend-api`.

## Reserved repositories

The unused backend Vite client and duplicate problem/contest services were
removed during hardening. Empty `evaluation-engine` and `ml-problems`
repositories should remain reserved or be archived once ownership is decided.

## Branch strategy recommendation

- Backend: protect `main`, use short-lived branches, PRs and immutable releases.
- Frontend: decide whether `develop` adds value. If retained, document promotion
  `feature → develop → release/main`; otherwise use protected trunk `main`.
- Documentation: protect `main`; owner-authorized direct pushes were used for
  bootstrap and this production-readiness synchronization.

No default branch should accept force pushes or bypass required checks for normal
delivery. Configure required checks, review requirements, and release approvals
before opening the public beta.
