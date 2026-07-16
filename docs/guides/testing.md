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

Verified release baseline: 31 unit/component tests, eight Playwright browser
journeys, strict TypeScript/lint, production build, zero dependency findings,
and a non-root Node 24 container. Coverage includes the OAuth redirect/cookie
proxy, browser-Python worker contract, live catalog normalization, local
progress/history, and free-beta contest restrictions.

Playwright uses deterministic product data for repeatable UI flows and includes
a real pinned-Pyodide acceptance path. The 2026-07-16 public smoke additionally
verified Vercel BFF → Render API → Atlas/Upstash, account lifecycle, the complete
126-problem list, 2/2 samples, and an 8/8 Easy practice submission in Chrome.

## Backend

```bash
npm ci
npm test -- --coverage
npm audit --omit=dev --audit-level=high
docker build -t katalume-backend .
```

Verified release baseline: 23 suites / 140 tests. CI enforces minimum global
coverage of 80% statements, 65% branches, 80% functions and 80% lines. Tests
cover catalog generation and seed idempotency in addition to session rotation/reuse/revocation, roles and
ownership, input guards, idempotency, job claim/retry/dead-letter, Judge0
polling/resources, contest scoring, content versioning, migrations/seeds,
account lifecycle, audit history, and Redis lifecycle.

## Security and documentation

Both code repositories scan complete Git history with pinned Gitleaks v8.30.1,
run production dependency audits, build images, and receive grouped Dependabot
updates. Documentation runs `mkdocs build --strict`.

## Remaining upgrade evidence

| Layer | Required external/staging evidence |
|---|---|
| Server execution | Real private Judge0 compile/runtime/TLE/memory verdicts |
| Performance | API/Mongo/Redis/jobs/Judge0 at 2× approved beta peak |
| Resilience | Worker kill/reclaim, Judge outage, deploy interruption |
| Recovery | Managed Mongo restore and application rollback drill |
| Observability | Alert delivery and incident correlation |
| Accessibility | Manual WCAG 2.2 AA plus production Core Web Vitals |
