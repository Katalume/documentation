# Testing

## Frontend

```bash
cd frontend
npm run lint
npm run typecheck
npm run test:unit
npm run build
CI=1 npm run test:e2e
```

Current verified baseline:

- 6 Vitest files / 21 tests
- 8 Playwright tests
- Production build green

Playwright forces mock mode. This is intentional for deterministic UI coverage,
but it does not validate the frontend/backend contract.

## Backend

```bash
cd backend-api
npm test
npm test -- --coverage
```

Current verified baseline:

- 13 suites / 91 tests
- 84.3% statements
- 67.27% branches
- 78.5% functions
- 84.47% lines

Runner and Judge0 integration coverage is low and Judge0 is mocked.

## Documentation

```bash
cd documentation
mkdocs build --strict
```

## Required launch test matrix

| Layer | Required evidence |
|---|---|
| Unit | Domain mapping, guards, auth/session, verdict normalization |
| API integration | Every endpoint, roles, ownership, invalid input, concurrency |
| Contract | Frontend client against live backend schemas |
| Execution | Real Judge0 success, compile/runtime/TLE/memory/internal failures |
| E2E | Staging signup → run → submit → progress → logout |
| Security | SAST, dependency, secret, container, auth and abuse testing |
| Performance | API, Mongo, queue and Judge0 at target concurrency |
| Accessibility | WCAG 2.2 AA manual + automated review |
| Resilience | Retry, worker crash, deploy interruption, backup restoration |

## CI gates required

- Zero high/critical production dependency findings
- Coverage thresholds that cannot regress silently
- OpenAPI/schema compatibility
- Migration dry run
- Container build and scan
- Staging smoke test after deploy
- Manual production promotion approval
