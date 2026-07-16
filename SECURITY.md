# Security policy

## Reporting

Do not open public issues for vulnerabilities that could expose accounts,
credentials, future server-only tests, user code, or infrastructure. Contact the repository
owners privately through the GitHub organization until a dedicated security
address is published.

Include affected repository/commit, reproduction steps, impact, and suggested
mitigation. Do not access data that is not yours or run destructive tests.

## Scope

Security-sensitive surfaces include authentication, authorization, session
cookies, admin operations, submitted code, browser workers, Judge0, test data, MongoDB,
Redis/PostgreSQL, CI/CD, analytics, and observability.

## Supported versions

During the public beta, only the latest backend `main` and the identical
frontend `main`/`develop` release tree are supported. This policy must be
replaced with a versioned support table at general availability.
