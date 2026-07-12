# Observability

## Service-level objectives

Set explicit beta targets before launch. Suggested starting objectives:

| Signal | Initial target |
|---|---|
| Frontend availability | 99.9% monthly |
| API non-execution availability | 99.9% monthly |
| API p95 read latency | < 500 ms |
| Submission accepted-to-final p95 | < 15 s under normal load |
| Stuck Pending submissions | 0 beyond reconciliation threshold |
| Queue oldest-job age | Alert before user SLA is breached |

Targets must be measured in staging/load tests and adjusted deliberately.

## Required telemetry

### Frontend

- Sentry exceptions/releases/source maps
- Web Vitals by route/device
- API error rate and fallback attempts
- Authentication and submission funnel events
- Consent-aware product analytics

### API

- Structured JSON logs with request ID, route, method, status, latency, user ID
  hash, environment, release, and safe error code
- HTTP request count/error/latency
- Mongo query latency/connection saturation
- Auth failures, refresh reuse/revocation, rate limits
- Submission creation/finalization/failure

### Queue and Judge0

- Queue depth, oldest age, throughput, retries, dead-letter count
- Worker busy/idle, job duration, crash/restart count
- Judge0 latency and verdict distribution
- Timeout, compile, runtime, memory, internal, and infrastructure failures
- Per-user/IP execution consumption and cost

## Alerting

Create actionable alerts for:

- elevated 5xx/auth/session failures;
- Mongo or Redis unavailable/saturated;
- queue age/depth breach;
- worker/Judge0 capacity loss;
- stuck Pending submissions;
- frontend error or Web Vital regression;
- backup failure;
- unusual signup/execution abuse or cost spike.

Every alert needs severity, owner, dashboard, diagnosis steps, and runbook link.

## Data handling

Never log passwords, cookies, access/refresh tokens, submitted source, hidden test
contents, Judge0 auth, database URIs, or unredacted request bodies. Define
retention and access controls for logs and traces.
