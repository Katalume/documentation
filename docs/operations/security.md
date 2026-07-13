# Security

## Threat model priorities

1. Account takeover and privileged Admin compromise
2. XSS/token theft from browser storage
3. Authorization bypass or user-data exposure
4. Untrusted-code escape, network access, or resource exhaustion
5. Hidden-test/content leakage
6. Queue/database/hosting credential compromise
7. Signup, execution, scraping, and cost abuse
8. Supply-chain and CI/CD compromise

## Controls already present

- Helmet on API
- Exact configured CORS origins
- Same-origin BFF and Secure/HttpOnly/SameSite access/refresh cookies
- Rotated/hashed/tracked Sessions with revocation and reuse detection
- Role and ownership enforcement
- Request/body/source/testcase/language limits and distributed/user quotas
- Judge0 auth header support
- Non-root Node 24 frontend/backend containers
- Production fail-fast service/secrets configuration
- Durable jobs, bounded resources/retries/dead-letter, request IDs/audit logs

## P0 hardening

- Add email verification/recovery and mandatory Admin MFA.
- Deploy Judge0 on private dedicated hosts.
- Configure Judge0 auth, deny network, bound resources/queues, disable unused APIs.
- Retain Node 24 and zero-finding production audit gates.
- Rotate all credentials ever exposed during development.
- Protect repository branches and production deployments.

## Web controls

- HTTPS everywhere with HSTS after domain validation
- Content Security Policy compatible with Monaco/Sentry
- Permissions Policy, Referrer Policy, frame restrictions
- CSRF protection when cross-site cookies are necessary
- Input/output encoding and runtime schema validation
- Safe remote avatar/upload policy
- Bot/signup protection and distributed quotas

## Supply chain

- Dependabot/Renovate and dependency review
- `npm audit` gate and license review
- Full-history Gitleaks scanning and SAST review
- SBOM per release
- Container image scanning and signed provenance
- Pin Actions and base images to reviewed versions/digests

## Privacy

Katalume stores identity, source code, submissions, progress, and potentially
analytics. Before public collection, publish privacy/retention policies,
implement export/deletion, minimize telemetry, restrict staff access, and define
breach notification procedures.
