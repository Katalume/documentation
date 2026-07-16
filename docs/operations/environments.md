# Environments

## Environment model

| Environment | Purpose | Execution | Data |
|---|---|---|---|
| Local mock | UI development and deterministic tests | Mock | Browser fixtures |
| Local live | API/integration work | Browser or local Judge0 | Local/in-memory services |
| Public free beta | Real practice traffic | Browser CPython/WASM | Atlas + Upstash free tiers |
| Future production | Ranked/SLO-backed product | Browser Run + isolated server Submit | Separate managed production services |

## Current public free beta

| Component | Current value |
|---|---|
| Frontend | `https://katalume.vercel.app` |
| Frontend project | Vercel `katalume` |
| Frontend mode | `live`, no mock fallback, browser execution |
| API | `https://katalume-api.onrender.com/api` |
| API platform | Render free web service `katalume-api` |
| MongoDB | Atlas free cluster, database `katalume` |
| Redis | Upstash free TLS database `Katalume` |
| Google OAuth callback | `https://katalume.vercel.app/api/auth/oauth/google/callback` |
| GitHub OAuth callback | `https://katalume.vercel.app/api/auth/oauth/github/callback` |

The API's `/ready` endpoint must report Mongo and Redis `true` and execution
`disabled`. The latter is expected in the free browser-practice profile.

Render shared outbound ranges currently required by Atlas are
`74.220.52.0/24` and `74.220.60.0/24`. Re-read Render's Connect → Outbound view
before changing region or service; do not assume these ranges are permanent.

## Future paid topology

```text
app.katalume.com         -> edge frontend/BFF
api.katalume.com         -> API load balancer
status.katalume.com      -> independent status page
MongoDB / Redis / Judge0 -> private or narrowly restricted networks
```

Paid staging and production must use separate projects, databases, queues,
Judge0 clusters, secrets, DSNs, analytics properties, and domains.

## Promotion policy

1. Merge to the canonical branch after required checks.
2. Produce one immutable release commit.
3. Deploy the configured artifact.
4. Verify `/health`, `/ready`, provider discovery, catalog count, BFF routing,
   security headers, login, and one real browser practice submission.
5. Observe quotas/errors before announcing the release.
6. Promote the previous known-good deployment if a gate regresses.

Never use mock fallback in the public beta or future production; it hides
outages and corrupts product truth.
