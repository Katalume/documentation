# Repository reference

## Active repositories

### `Katalume/frontend`

- Next.js/React product
- Default branch: `develop`
- Release mirror/production branch: `main`
- Public repository; browser-practice PR 40 is merged to `develop` and release
  PR 41 is merged to `main`

Key paths:

- `src/app` — routes and UI
- `src/app/components` — shared product components
- `src/lib/api.ts` — live/mock API boundary
- `src/lib/browserPython.ts` — disposable browser CPython runner
- `public/python-runner.worker.mjs` — isolated worker entrypoint
- `src/data/problem-catalog.json` — versioned local practice suites
- `src/app/api/[...path]` — same-origin BFF
- `src/context/AuthContext.tsx` — session state
- `src/proxy.ts` — route/session validation
- `e2e` — Playwright flows

### `Katalume/backend-api`

- Express/Mongoose API plus disabled-by-default Judge0 upgrade path
- Default branch: `main`
- Public repository; catalog/import/browser-practice PRs through 31 are merged
  to `main`

Key paths:

- `src/routes` — endpoint map
- `src/controllers` — request/domain orchestration
- `src/models` — Mongo schemas
- `src/services/judge.service.js` — Judge0 integration
- `src/services/evaluation.service.js` — durable queue/job lifecycle
- `src/workers` — evaluation worker entrypoint
- `scripts/generateProblemCatalog.js` — deterministic catalog generator
- `data/problem-catalog.json` — 126 versioned problems and testcases
- `src/routes/import.routes.js` — authenticated problems-as-code synchronization
- `src/services/problemImport.service.js` — atomic versioned catalog import
- `tests` — Jest/Supertest integration tests

### `Katalume/ml-problems`

- Canonical problems-as-code source for pipeline-managed content
- Default branch: `main`
- 72 problems / 1,353 testcases at the current release
- CI validates the exact browser `solve(payload)` sandbox contract
- A secret-isolated artifact workflow synchronizes the catalog into the API

Key paths:

- `problems/<slug>` — metadata, statement, starter, reference solution, tests
- `tools/validate.py` — schema, sandbox, and reference-solution validation
- `tools/export.py` — authoritative import bundle with removal archival
- `.github/workflows/sync.yml` — isolated build and production sync jobs

### `Katalume/documentation`

- Product, engineering, API, operations, and launch source of truth
- Default branch: `main`
- Built with MkDocs Material

All six organization repositories are public. `ml-problems` is active;
`evaluation-engine` remains reserved for a future isolated execution service.

## Reserved repositories

The unused backend Vite client and duplicate problem/contest services were
removed during hardening. The empty `evaluation-engine` repository remains
reserved until isolated server execution is funded.

## Branch strategy recommendation

- Backend: protect `main`, use short-lived branches, PRs and immutable releases.
- Frontend: decide whether `develop` adds value. If retained, document promotion
  `feature → develop → release/main`; otherwise use protected trunk `main`.
- Documentation: protect `main`; owner-authorized direct pushes were used for
  bootstrap and this production-readiness synchronization.

No default branch should accept force pushes or bypass required checks for normal
delivery. Configure required checks, review requirements, and release approvals
before general availability.
