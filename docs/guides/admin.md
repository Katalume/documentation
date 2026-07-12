# Administrator guide

## Provision an administrator

Administrators cannot self-assign the Admin role through signup.

```bash
cd backend-api
ADMIN_EMAIL=admin@example.com \
ADMIN_USERNAME=admin \
ADMIN_PASSWORD='use-a-secret-manager-value' \
node scripts/seedAdmin.js
```

Run this against the intended environment with least-privilege database access.
Never place the password in shell history or repository files in production.

## Access

1. Sign in through `/login`.
2. Confirm the account has the `Admin` role.
3. Open `/admin` from the navigation.

## Content operations

### Problems

Provide title, description, difficulty, tags, starter code, constraints, sample
tests, hidden tests, and editorial data. Validate that:

- every hidden test has an expected output;
- resource limits are realistic;
- examples match the statement;
- starter code uses the supported language/runtime;
- the editorial is correct and is not publicly exposed before its unlock rule.

### Contests

Provide title, description, start/end timestamps, and problem IDs. Use UTC in
storage and verify the rendered timezone. Do not run public contests until
submission attribution and scoring are implemented.

### Learning tracks

Provide a unique title, description, problem tags, ordered lessons, and display
order. Verify that tags match real catalog tags.

## Production controls still required

- Admin MFA and shorter privileged sessions
- Audit log with actor, action, target, before/after, timestamp, and request ID
- Draft/review/publish lifecycle
- Two-person approval for destructive or high-impact changes
- Soft deletion and restore
- Rate limits and notifications for bulk actions
