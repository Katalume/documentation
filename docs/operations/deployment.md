# Deployment

## Frontend

### Required Vercel production configuration

```dotenv
NEXT_PUBLIC_API_MODE=live
NEXT_PUBLIC_API_FALLBACK_TO_MOCK=false
BACKEND_API_URL=https://api.<private-domain>/api
NEXT_PUBLIC_SITE_URL=https://<public-domain>
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

Both repositories build Node 24 Alpine images and run as non-root. CI rebuilds
them from clean Linux lockfiles. The API exposes `/health` and Mongo/Redis
`/ready`, handles bounded SIGTERM shutdown, and must receive platform
CPU/memory/replica limits. Pin deployment digests after registry publication.

### Required services

- Managed MongoDB with TLS/backups/PITR
- Shared Redis for distributed limits
- Managed MongoDB durable EvaluationJob collection
- Stateless API replicas behind TLS load balancer
- Separate worker replicas
- Private Judge0 CE 1.13.1 deployment with its PostgreSQL and Redis

### Required secrets

- Mongo URI
- Strong independent JWT access/refresh secrets or key material
- Judge0 authentication token
- Redis credentials/TLS settings
- Cookie same-site policy (normally host-only through the BFF)
- Exact CORS origins
- Observability DSNs/tokens

### Deployment sequence

1. Validate environment and secret presence without printing values.
2. Run `npm run migrate`, then idempotent `npm run seed` when approved content is required.
3. Deploy API instances.
4. Deploy `npm run worker` replicas paused, validate connectivity, then resume.
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
