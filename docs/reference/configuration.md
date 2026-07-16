# Configuration reference

## Frontend and BFF

| Variable | Public free beta | Purpose |
|---|---:|---|
| `NEXT_PUBLIC_API_MODE` | `live` | Select live adapter |
| `NEXT_PUBLIC_API_FALLBACK_TO_MOCK` | `false` | Fail closed on API outages |
| `BACKEND_API_URL` | `https://katalume-api.onrender.com/api` | Server-only API base |
| `NEXT_PUBLIC_API_URL` | Blank | Direct-browser diagnostic override only |
| `NEXT_PUBLIC_SITE_URL` | `https://katalume.vercel.app` | Canonical public origin |
| `NEXT_PUBLIC_EXECUTION_MODE` | `browser` | Enable local CPython practice |
| `NEXT_PUBLIC_APP_ENV` | `production` | Environment label |
| `NEXT_PUBLIC_API_RETRY_COUNT` | Optional | Idempotent retry count, default 2 |
| `NEXT_PUBLIC_API_TIMEOUT_MS` | Optional | Request timeout, default 8000 ms |
| `NEXT_PUBLIC_SENTRY_DSN` / `SENTRY_DSN` | Optional until configured | Error capture |

Only `NEXT_PUBLIC_*` values reach browser bundles. Never place secrets there.
Production compilation refuses mock mode/fallback, missing backend/site URLs,
or non-HTTPS URLs.

## Backend public free-beta profile

| Variable | Value/policy | Purpose |
|---|---:|---|
| `NODE_ENV` | `production` | Fail-fast security behavior |
| `EXECUTION_MODE` | `disabled` | Reject server execution routes |
| `BACKEND_PORT` | Platform (`PORT` supported) | API listener |
| `TRUST_PROXY` | `1` on Render | Trusted proxy hops |
| `MONGO_URI` / `MONGO_POOL_SIZE` | Required / `5` | Atlas TLS database and pool |
| `REDIS_URL` | Required `rediss://` TCP URL | Distributed rate limits |
| `JWT_ACCESS_SECRET` / `JWT_REFRESH_SECRET` | Required and independent | Session signing |
| `COOKIE_DOMAIN` | Blank | Host-only public-site cookies via BFF |
| `COOKIE_SAME_SITE` | `lax` | OAuth-compatible cookie boundary |
| `ALLOW_BEARER_AUTH` | `false` | Disable browser bearer path |
| `CORS_ORIGIN` | Exact Vercel origin | Direct-origin boundary |
| `FRONTEND_URL` | Exact Vercel origin | Redirect target |
| `OAUTH_CALLBACK_BASE_URL` | Exact Vercel origin | Same-origin OAuth callback base |
| `GOOGLE_CLIENT_ID/SECRET` | Required for Google | Provider credentials |
| `GITHUB_CLIENT_ID/SECRET` | Both or neither | Provider stays hidden if incomplete |

The Upstash REST token is not a Redis TCP password. Copy the TCP `REDIS_URL`
from Upstash and validate it with `PING` without printing the value.

## Future Judge0 profile

Set `EXECUTION_MODE=judge0`, `JUDGE0_URL`, `JUDGE0_AUTH_TOKEN`, allowed language
IDs, timeouts, polling, bounded concurrency, source/resource limits, and worker
retry/retention policy only in an isolated server-execution environment. Deny
network access, restrict port 2358, and never commit values.
