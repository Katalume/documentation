# Backend architecture

## Stack

- Node.js and Express 5
- Mongoose and MongoDB
- JWT access/refresh tokens
- Judge0 CE 1.13.1
- Helmet, CORS, express-rate-limit, and express-validator
- Jest, Supertest, and mongodb-memory-server

## Layers

| Layer | Location | Responsibility |
|---|---|---|
| Routing | `src/routes` | URL/method composition and middleware |
| Middleware | `src/middleware` | Authentication, roles, throttling |
| Controllers | `src/controllers` | Request validation/orchestration/response |
| Models | `src/models` | MongoDB schemas and indexes |
| Execution service | `src/services/judge.service.js` | Judge0 HTTP integration |
| Utilities | `src/utils` | JWT, logging, slugging, guards, response helpers |
| Scripts | `scripts` | Seed/admin/development bootstrap |

Several older service modules and the `backend-api/client` Vite application are
unused and should be archived or removed after launch stabilization.

## Security controls implemented

- Production fails fast without access and refresh secrets.
- Access token expiry: 15 minutes.
- Refresh/session cookie expiry: 7 days.
- Secure/httpOnly/sameSite cookie options.
- Role-based Admin authorization.
- Ownership checks on user records.
- Global, auth, and execution rate limits.
- 100 KB JSON body limit.
- Judge language allow list and 64 KB source limit.
- Judge0 authentication header support.
- Hidden test contents are not returned by problem APIs.

## Production gaps

### Execution

- Synchronous sequential Judge0 requests block API workers.
- No Axios timeout or durable queue.
- Per-test CPU/memory settings are dropped by the judge service.
- Result status enum cannot represent every Judge0 verdict.
- Queue/worker files are placeholders and Redis is not installed as a dependency.

### Authentication

- Refresh JWTs are stateless and cannot be revoked.
- Refresh is not rotated; reuse cannot be detected.
- No email verification, password reset/change, MFA, or device sessions.

### Data and scale

- Missing pagination and several compound indexes.
- Progress/profile endpoints load all user submissions and all problems.
- Delete paths leave related data behind.
- Multi-document content writes are not transactional.
- Contest submission attribution/scoring is incomplete.

### Operations

- Console-only logging; no request IDs, metrics, tracing, or backend Sentry.
- Health endpoint does not test dependencies.
- In-memory rate limits do not coordinate across instances.
- `trust proxy` is not configured for a production load balancer.
