# Configuration reference

## Frontend and BFF

| Variable | Production | Purpose |
|---|---:|---|
| `NEXT_PUBLIC_API_MODE` | Required: `live` | Select live adapter |
| `NEXT_PUBLIC_API_FALLBACK_TO_MOCK` | Required: `false` | Fail closed on outages |
| `BACKEND_API_URL` | Required, HTTPS | Server-only private API base ending `/api` |
| `NEXT_PUBLIC_API_URL` | Leave blank | Direct-browser diagnostic override only |
| `NEXT_PUBLIC_SITE_URL` | Required, HTTPS | Canonical public origin |
| `NEXT_PUBLIC_API_RETRY_COUNT` | Recommended | Idempotent retry count, default 2 |
| `NEXT_PUBLIC_API_TIMEOUT_MS` | Recommended | Per-request timeout, default 8000 ms |
| `NEXT_PUBLIC_APP_ENV` | Required | Environment telemetry label |
| `NEXT_PUBLIC_ANALYTICS_ENDPOINT` | Optional | Approved consent-aware collector |
| `NEXT_PUBLIC_SENTRY_DSN` / `SENTRY_DSN` | Recommended | Client/server error capture |
| `SENTRY_ORG/PROJECT/AUTH_TOKEN` | Build secret/config | Release/source maps |

Only `NEXT_PUBLIC_*` values reach browser bundles. Never place secrets there.
Vercel production compilation refuses mock mode/fallback, missing backend/site
URLs, or non-HTTPS URLs.

## Backend API and worker

| Variable | Production | Purpose |
|---|---:|---|
| `NODE_ENV` | `production` | Fail-fast security behavior |
| `BACKEND_PORT` | Platform | API port, default 5001 |
| `TRUST_PROXY` | Required | Trusted proxy hop count |
| `MONGO_URI` / `MONGO_POOL_SIZE` | Required | TLS database and pool |
| `REDIS_URL` | Required | Distributed rate-limit store |
| `JWT_ACCESS_SECRET` / `JWT_REFRESH_SECRET` | Required | Independent high-entropy signing secrets |
| `COOKIE_DOMAIN` | Normally blank | BFF issues host-only public-site cookies |
| `COOKIE_SAME_SITE` | `strict` | Cookie CSRF boundary |
| `ALLOW_BEARER_AUTH` | `false` | Disable browser bearer path |
| `CORS_ORIGIN` | Required | Exact administrative/direct origins |
| `JUDGE0_URL` / `JUDGE0_AUTH_TOKEN` | Required | Private authenticated executor |
| `JUDGE0_TIMEOUT_MS` | Recommended | Request/poll deadline, default 20000 |
| `JUDGE0_POLL_INTERVAL_MS` | Recommended | Poll interval, default 250 |
| `JUDGE0_CONCURRENCY` | Required capacity choice | Testcase concurrency, default 4 |
| `ALLOWED_LANGUAGE_IDS` | Required | Execution allow list |
| `MAX_CODE_SIZE` | Recommended | Source bytes, default 65536 |
| `EVALUATION_WORKER_POLL_MS` | Recommended | Empty-queue poll delay |
| `EVALUATION_JOB_MAX_ATTEMPTS` | Recommended | Retry/dead-letter bound, default 3 |
| `EVALUATION_JOB_RETENTION_DAYS` | Policy | Job/source TTL, default 7 |

Production configuration refuses to start without Mongo, Redis, exact CORS,
Judge0 authentication, and both JWT secrets.

## Judge0

Set strong `AUTHN_TOKEN`, Redis/Postgres credentials and secret key; bound queue,
CPU, wall, memory, process, output and extracted-file limits; deny network;
restrict IP/network access; disable unused callbacks, compiler arguments and
additional files. Never commit environment values.
