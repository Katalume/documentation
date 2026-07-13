# Testing

## Frontend

```bash
npm ci
npm run lint
npm run typecheck
npm run test:unit
npm run build
npm run test:e2e
npm run audit:production
docker build -t katalume-frontend .
```

Verified source baseline: eight Vitest files with live BFF/session/run/submit
contract coverage, 26 unit/component tests, eight Playwright browser journeys,
strict TypeScript/lint, production build, zero production dependency findings,
and a non-root Node 24 container.

Playwright deliberately uses mock product data for deterministic visual/product
flows. A separate isolated container smoke exercised the real same-origin BFF,
HttpOnly session, Mongo, Redis, API, durable worker queue and simulated Judge0
terminal contract.

## Backend

```bash
npm ci
npm test -- --coverage
npm audit --omit=dev --audit-level=high
docker build -t katalume-backend .
```

Verified baseline: 20 suites / 115 tests; 83.13% statements, 68.32% branches,
80.71% functions and 85.29% lines. CI enforces minimum global coverage of 80%
statements, 65% branches, 80% functions and 80% lines. Tests cover session rotation/reuse/revocation, roles and
ownership, input guards, idempotency, job claim/retry/dead-letter, Judge0
polling/resources, contest scoring, content versioning, migrations/seeds,
account lifecycle, audit history, and Redis lifecycle.

## Security and documentation

Both code repositories scan complete Git history with pinned Gitleaks v8.30.1,
run production dependency audits, build images, and receive grouped Dependabot
updates. Documentation runs `mkdocs build --strict`.

## Remaining launch evidence

| Layer | Required external/staging evidence |
|---|---|
| Execution | Real private Judge0 compile/runtime/TLE/memory verdicts |
| Performance | API/Mongo/Redis/jobs/Judge0 at 2× approved beta peak |
| Resilience | Worker kill/reclaim, Judge outage, deploy interruption |
| Recovery | Managed Mongo restore and application rollback drill |
| Observability | Alert delivery and incident correlation |
| Accessibility | Manual WCAG 2.2 AA plus production Core Web Vitals |
