# Deployment

## Public free-beta profile

### Frontend: Vercel

```dotenv
NEXT_PUBLIC_API_MODE=live
NEXT_PUBLIC_API_FALLBACK_TO_MOCK=false
BACKEND_API_URL=https://katalume-api.onrender.com/api
NEXT_PUBLIC_SITE_URL=https://katalume.vercel.app
NEXT_PUBLIC_EXECUTION_MODE=browser
NEXT_PUBLIC_APP_ENV=production
```

Deploy `main` to production. `develop` is the integration branch and must have
the same tree as `main` at release. Apply environment changes by redeploying the
known-good production artifact; Vercel does not retroactively inject them.

```bash
npm ci
npm run lint
npm run typecheck
npm run test:unit
npm run build
CI=1 npm run test:e2e
npm audit --omit=dev
```

The postinstall step copies the pinned Pyodide runtime to `public/pyodide`.
Docker dependency stages use `npm ci --ignore-scripts`; the builder must run
`npm run postinstall` before `npm run build`.

### Backend: Render

The free Blueprint deploys Docker service `katalume-api` from backend `main`.
The public profile uses:

```dotenv
NODE_ENV=production
EXECUTION_MODE=disabled
TRUST_PROXY=1
ALLOW_BEARER_AUTH=false
COOKIE_DOMAIN=
COOKIE_SAME_SITE=lax
MONGO_POOL_SIZE=5
```

Set Mongo, Redis, JWT, origin, frontend, OAuth, and
`PROBLEMS_IMPORT_TOKEN` values in Render; never place their values in
`render.yaml` or shell history. `REDIS_URL` must be Upstash's TCP TLS URL
(`rediss://...`), not its REST token.

```bash
npm ci
npm test
npm run migrate
npm run seed
```

The seed and repository import are versioned and idempotent. Current expected
production totals are 198 problems and 4,839 active testcases.

`ml-problems` stores matching `KATALUME_IMPORT_URL` and
`KATALUME_IMPORT_TOKEN` GitHub secrets. Its workflow validates repository code
without secrets, uploads an immutable bundle, and imports it from a separate
secret-bearing job that never checks out or executes repository code.

### Network controls

Atlas must allow the operator's current IP plus the exact shared outbound CIDRs
shown by Render. The launch service currently reports `74.220.52.0/24` and
`74.220.60.0/24`. Never use `0.0.0.0/0`. Reconcile the list whenever the Render
region or service is recreated.

### Post-deploy checks

```bash
curl -fsS https://katalume-api.onrender.com/health
curl -fsS https://katalume-api.onrender.com/ready
curl -fsS 'https://katalume.vercel.app/api/problems?limit=200'
curl -fsS https://katalume.vercel.app/api/auth/providers
```

Expected results: health and ready `200`, Mongo/Redis true, server execution
disabled, 198 BFF problems, and Google plus GitHub listed. Verify
`GET /api/problems/:slug/practice` and complete an actual browser Run and Submit
after every execution-runtime or content-delivery change.

## Free-tier behavior

Render sleeps after inactivity, so the first API-backed action can be delayed by
roughly a minute. Do not create artificial keep-alive traffic. Surface a clear
wake-up/retry experience and upgrade when real usage warrants it.

## Future server-execution deployment

When paid capacity is approved, add managed Mongo/Redis, stateless API replicas,
separate workers, and private authenticated Judge0 with its own Redis/Postgres.
Set `EXECUTION_MODE=judge0` only after resource limits, denied network egress,
capacity, alerts, recovery, and rollback are proven. Judge0 must never share an
API/data trust zone or expose port 2358 publicly.

## Rollback

- Frontend: promote the previous known-good Vercel production deployment.
- API: redeploy the previous immutable commit/image while keeping schema
  compatibility.
- Configuration: restore the last verified environment revision, then rerun
  health, readiness, BFF, auth, and catalog checks.
- Data: use reversible migrations or targeted repair; never restore production
  data without an incident plan.
