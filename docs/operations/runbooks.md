# Runbooks

## API elevated 5xx

1. Confirm scope by release, route, region, and dependency.
2. Check Mongo, Redis, and Judge0 health separately.
3. Compare against the last deployment and configuration change.
4. Stop production promotion; roll back if release-correlated.
5. Preserve request IDs and sanitized error evidence.
6. Communicate status and open an incident record.

## Queue backlog

1. Inspect queue depth, oldest age, worker count, retry/dead-letter rate.
2. Verify Judge0 capacity and Redis health.
3. Pause new submissions or tighten quotas before saturation.
4. Scale healthy workers only within Judge0/database capacity.
5. Never delete jobs without reconciling Pending submission records.
6. Drain, reconcile, and document root cause.

## Stuck Pending submissions

1. Query records older than the execution SLA.
2. Correlate submission IDs to job state.
3. If job is absent, re-enqueue with the same idempotency key.
4. If retries are exhausted, finalize as Internal Error and notify the user.
5. Record repair counts and fix the failure mode.

## Judge0 unavailable

1. Stop accepting or queue new execution within bounded capacity.
2. Verify server, workers, internal Redis/PostgreSQL, disk, and host isolation.
3. Fail safely; never execute user code in the API process.
4. Restore capacity, run canary submissions, then resume consumers gradually.

## MongoDB unavailable

1. Put writes/execution admission into controlled failure mode.
2. Check provider status, connections, network/TLS, and recent changes.
3. Do not promote failover until consistency implications are understood.
4. Validate reads/writes and queue reconciliation after recovery.

## Credential exposure

1. Revoke/rotate immediately; do not wait for incident analysis.
2. Search logs, CI, Git history, artifacts, screenshots, and chat exposure.
3. Invalidate sessions where relevant.
4. Review access/audit logs for misuse.
5. Purge or rewrite history only as a secondary containment step.
6. Document scope, notification duties, and prevention.

## Rollback

1. Declare incident and freeze unrelated deploys.
2. Roll back frontend/API/worker independently using immutable releases.
3. Pause queue if worker compatibility is uncertain.
4. Run health and critical-journey smoke tests.
5. Observe before closing; write a blameless postmortem.

## Backup restore drill

1. Restore the latest backup into an isolated environment.
2. Validate users/content/submissions/indexes and application startup.
3. Measure RPO/RTO against objectives.
4. Destroy restored sensitive data securely.
5. Record evidence and remediate failures.
