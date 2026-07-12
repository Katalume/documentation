# Environments

## Required environment model

| Environment | Purpose | Data | External access |
|---|---|---|---|
| Local mock | UI development/test | Browser sample data | Developer only |
| Local live | Contract/integration | In-memory or local Mongo/Judge | Developer only |
| Staging | Production-like validation | Synthetic/non-production | Team/test users |
| Production | Public product | Real user data | Public frontend; restricted internals |

Staging and production must use separate projects, databases, queues, Judge0
clusters, secrets, DSNs, analytics properties, and domains.

## Current hosted state

- Vercel project: `frontend`
- Inspected production commit: `71cf79b`
- Production status: Ready
- Custom domain: not observed; aliases are Vercel domains
- Production environment variables: none
- Effective frontend mode: `mock`
- Backend production deployment: not recorded

## Production topology recommendation

```text
app.mlboost.<tld>       -> Vercel frontend
api.mlboost.<tld>       -> API load balancer
status.mlboost.<tld>    -> independent status page
MongoDB / Redis / Judge0 -> private networks only
```

## Promotion policy

1. Merge to canonical branch.
2. Produce one immutable release/commit.
3. Deploy to staging.
4. Run migrations, contract tests, live E2E, security checks, and smoke tests.
5. Approve production promotion.
6. Deploy the same artifact/configured image.
7. Run post-deploy smoke tests and observe error/SLO dashboards.
8. Roll back automatically or manually if thresholds fail.

Never use mock fallback in staging or production; it hides outages and corrupts
product truth.
