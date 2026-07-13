# Backend architecture

## Stack and processes

- Node.js 24 and Express 5
- Mongoose/MongoDB for product data, Sessions, audit history, and durable jobs
- Redis-backed distributed rate limits
- Separate `npm start` API and `npm run worker` evaluation processes
- Asynchronous authenticated Judge0 integration
- Helmet, strict CORS, global unsafe-key rejection, express-validator
- Jest/Supertest/mongodb-memory-server with enforced coverage thresholds

## Implemented controls

- Production fail-fast secrets/configuration; private cookie-only browser auth
- 15-minute access and seven-day rotating refresh cookies
- Hashed server-side Sessions, revocation, logout-all, and reuse detection
- Password change, account export, and cascading account deletion
- Role/ownership checks and one-year Admin mutation audit events
- Request IDs, structured JSON logs, liveness and Mongo/Redis readiness
- Distributed global/auth limits and authenticated per-user execution quotas
- 100 KB HTTP body, 64 KB source, 100-testcase and input/output limits
- Judge0 timeout/polling, bounded concurrency, CPU/memory/wall/file limits
- Durable atomic job claims, heartbeats, retries, dead letter and reconciliation
- Idempotent submissions and owner-only status/cancellation
- Contest window/registration/problem enforcement and idempotent scoring
- Version-switched testcase publication, migrations, indexes, and pagination
- Node 24 non-root image, dependency audit, secret history scan, Dependabot

## Remaining production dependencies

- Managed production MongoDB/Redis and private Judge0 provisioning
- Central metrics/tracing/alerts and backend error aggregation
- Backup restoration, load, resilience and rollback drills
- Email verification/recovery delivery and mandatory Admin MFA
- Owner-approved retention/legal/support policies
- Legacy Judge0 Redis/Postgres values found in removed history must be confirmed
  rotated for any environment that ever used them
