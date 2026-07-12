# Configuration reference

## Frontend

| Variable | Required in production | Purpose |
|---|---:|---|
| `NEXT_PUBLIC_API_MODE` | Yes | `live` in production; `mock`/`auto` only for development |
| `NEXT_PUBLIC_API_URL` | Yes | Backend base URL ending in `/api` |
| `NEXT_PUBLIC_API_FALLBACK_TO_MOCK` | Yes | Must be `false` in staging/production |
| `NEXT_PUBLIC_API_RETRY_COUNT` | Recommended | GET/HEAD retry count; default 2 |
| `NEXT_PUBLIC_API_TIMEOUT_MS` | Recommended | Browser request timeout; default 8000 |
| `NEXT_PUBLIC_APP_ENV` | Yes | Environment tag such as production/staging |
| `NEXT_PUBLIC_ANALYTICS_ENDPOINT` | Optional | Approved analytics collector |
| `NEXT_PUBLIC_SENTRY_DSN` | Recommended | Browser Sentry DSN |
| `NEXT_PUBLIC_SENTRY_TRACES_SAMPLE_RATE` | Recommended | Browser trace sampling |
| `SENTRY_DSN` | Recommended | Server/edge Sentry DSN |
| `SENTRY_TRACES_SAMPLE_RATE` | Recommended | Server trace sampling |
| `SENTRY_ORG` | Build only | Source-map release organization |
| `SENTRY_PROJECT` | Build only | Source-map project |
| `SENTRY_AUTH_TOKEN` | Build secret | Release/source-map upload token |

All `NEXT_PUBLIC_*` values are visible to browsers and must never contain
secrets.

## Backend API

| Variable | Required in production | Purpose |
|---|---:|---|
| `NODE_ENV` | Yes | Must be `production` |
| `BACKEND_PORT` | Platform-dependent | API listen port; default 5001 |
| `MONGO_URI` | Yes | Authenticated TLS Mongo connection |
| `JWT_ACCESS_SECRET` | Yes | Access-token signing secret |
| `JWT_REFRESH_SECRET` | Yes | Refresh-token signing secret |
| `COOKIE_DOMAIN` | Topology-dependent | Shared parent domain for cookie |
| `COOKIE_SAME_SITE` | Yes | `strict`, `lax`, or carefully reviewed `none` |
| `CORS_ORIGIN` | Yes | Comma-separated exact frontend origins |
| `JUDGE0_URL` | Yes | Private Judge0 URL |
| `JUDGE0_AUTH_TOKEN` | Yes | Judge0 `X-Auth-Token` value |
| `ALLOWED_LANGUAGE_IDS` | Yes | Comma-separated Judge0 language IDs |
| `MAX_CODE_SIZE` | Recommended | Maximum source bytes; default 65536 |
| `REDIS_HOST` | Queue target | Queue/limiter host |
| `REDIS_PORT` | Queue target | Queue/limiter port |

The production queue will also require TLS/password/URL, namespace, retry,
concurrency, and dead-letter configuration that is not yet represented.

## Judge0

At minimum set:

- `AUTHN_TOKEN` and matching backend `JUDGE0_AUTH_TOKEN`
- strong `REDIS_PASSWORD` and `POSTGRES_PASSWORD`
- stable `SECRET_KEY_BASE`
- bounded `COUNT`, `MAX_QUEUE_SIZE`, CPU, wall, memory, process, output, and
  extracted-file limits
- `ENABLE_NETWORK=false` and restricted `ALLOW_ENABLE_NETWORK`
- private `ALLOW_IP`/network policy
- unused compiler options, arguments, callbacks, and additional files disabled

Never commit environment-specific secret values.
