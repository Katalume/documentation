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
- httpOnly secure refresh/session cookie support
- Short-lived bearer token
- Role and ownership enforcement
- Request body, source size, language, and rate limits
- Judge0 auth header support
- Non-root API container
- Secret placeholders and production fail-fast JWT config

## P0 hardening

- Rotate and server-track refresh sessions; revoke on logout/reuse.
- Remove persistent bearer tokens from localStorage.
- Add email verification, reset/change, Admin MFA, and device sessions.
- Repair live contract and isolate Judge0 on private dedicated hosts.
- Configure Judge0 auth, deny network, bound resources/queues, disable unused APIs.
- Upgrade Node 20 to supported Node 24 LTS.
- Remediate frontend dependency advisories; block high/critical findings.
- Rotate all credentials ever exposed during development.
- Protect GitHub branches and production deployments.

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
- CodeQL/SAST and secret scanning/push protection
- SBOM per release
- Container image scanning and signed provenance
- Pin Actions and base images to reviewed versions/digests

## Privacy

MLBoost stores identity, source code, submissions, progress, and potentially
analytics. Before public collection, publish privacy/retention policies,
implement export/deletion, minimize telemetry, restrict staff access, and define
breach notification procedures.
