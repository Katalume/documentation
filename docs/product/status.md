# Current product status

**Snapshot:** 2026-07-16

## Public free beta

- Frontend: [https://katalume.vercel.app](https://katalume.vercel.app), Vercel
  production deployment `dpl_J9yMYKKKMuvDSSAT6EohP2zYRa2G` (`Ready`)
- API: [https://katalume-api.onrender.com](https://katalume-api.onrender.com),
  Render free web service (`live`)
- Data: MongoDB Atlas free cluster with 126 published problems and 3,486 active,
  deterministic testcases
- Distributed limits: Upstash Redis free tier over TLS
- Authentication: email/password plus Google and GitHub OAuth. Both external
  providers are configured in production through the same-origin BFF.
- Execution: pinned local CPython/Pyodide worker in the browser. The public API
  intentionally reports `execution: disabled` and rejects server run/submit.

The free beta is intentionally a practice product. Browser execution records
progress and history locally; ranked contests and durable server verdicts stay
disabled until isolated execution capacity is funded and proven.

## Verified release evidence

- Frontend PRs 40 and 41 merged with lint, typecheck, 31 unit tests, production
  build, 8 Playwright journeys, Gitleaks, dependency audit, and Vercel checks
- Backend PR 28 merged with 23 suites / 140 tests and Gitleaks
- Public `/health` and `/ready` return `200`; readiness reports Mongo and Redis
  healthy
- Public same-origin BFF returns all 126 problems and exposes Google and GitHub
  from `/api/auth/providers`
- GitHub OAuth returns the GitHub authorization flow with callback
  `https://katalume.vercel.app/api/auth/oauth/github/callback`
- Real Chrome smoke: account lifecycle, 126-problem catalog, local CPython
  compile, 2/2 sample tests, and 8/8 Easy practice tests accepted
- Disposable launch account and credentials removed after the smoke test
- `main` and `develop` frontend trees are identical at release

## Free-tier constraints

- Render sleeps after inactivity and may take roughly a minute to wake.
- Render shared outbound CIDRs are allow-listed in Atlas; Atlas is not open to
  `0.0.0.0/0`.
- Free Atlas, Upstash, Render, and Vercel quotas are hard launch limits, not an
  SLO-backed production platform.
- Central error monitoring, email verification/recovery, backup restore drills,
  load tests, and formal on-call remain open.

## Paid migration path

The durable Mongo EvaluationJob worker and private authenticated Judge0 path
remain in the backend behind `EXECUTION_MODE=judge0`. Moving to paid execution
requires isolated hosts, queue/worker deployment, Judge0 stores, network denial,
capacity tests, monitoring, and recovery evidence. The browser practice path can
remain as a low-latency Run mode after server Submit is enabled.
