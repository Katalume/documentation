# Deployment

## Frontend

### Required Vercel production configuration

```dotenv
NEXT_PUBLIC_API_MODE=live
NEXT_PUBLIC_API_FALLBACK_TO_MOCK=false
NEXT_PUBLIC_API_URL=https://api.<domain>/api
NEXT_PUBLIC_APP_ENV=production
NEXT_PUBLIC_API_RETRY_COUNT=2
NEXT_PUBLIC_API_TIMEOUT_MS=8000
NEXT_PUBLIC_SENTRY_DSN=<dsn>
SENTRY_DSN=<server-dsn>
SENTRY_ORG=<org>
SENTRY_PROJECT=<project>
SENTRY_AUTH_TOKEN=<secret>
```

Add an approved analytics endpoint only after privacy/consent review.

### Release checks

```bash
npm ci
npm run lint
npm run typecheck
npm run test:unit
npm run build
CI=1 npm run test:e2e
```

## Backend

### Container baseline

- Upgrade `node:20-alpine` to a pinned Node 24 LTS image/digest.
- Build and scan the image in CI.
- Run as non-root (already configured).
- Add liveness/readiness health checks.
- Set CPU/memory/connection limits.
- Send SIGTERM and allow bounded graceful shutdown.

### Required services

- Managed MongoDB with TLS/backups/PITR
- Shared Redis for queue and distributed limits
- Stateless API replicas behind TLS load balancer
- Separate worker replicas
- Private Judge0 CE 1.13.1 deployment with its PostgreSQL and Redis

### Required secrets

- Mongo URI
- Strong independent JWT access/refresh secrets or key material
- Judge0 authentication token
- Queue Redis credentials/TLS settings
- Cookie domain/same-site policy
- Exact CORS origins
- Observability DSNs/tokens

### Deployment sequence

1. Validate environment and secret presence without printing values.
2. Apply backward-compatible migrations/index changes.
3. Deploy API instances.
4. Deploy workers paused, validate connectivity, then resume consumption.
5. Run readiness and contract smoke tests.
6. Shift traffic gradually.
7. Observe latency, error, queue, judge, database, and cost signals.

## Judge0 isolation

Judge0 runs privileged containers. Deploy on dedicated, hardened execution hosts;
never on the API/database host. Keep port `2358` private, set `AUTHN_TOKEN`, deny
worker network egress, cap queue/workers/resources, rotate Redis/Postgres
passwords, and disable unused compiler arguments/callback/additional-file
features.

## Rollback

- Frontend: promote previous known-good Vercel deployment.
- API: restore previous immutable image while maintaining schema compatibility.
- Workers: pause queue, deploy previous worker, resume after health verification.
- Data: never “roll back” by restoring production data without an incident plan;
  use reversible migrations or targeted repair.
