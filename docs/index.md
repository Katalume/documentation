# MLBoost documentation

MLBoost is a browser-based platform for practicing machine-learning and
data-science problems: **LeetCode rigor with Kaggle depth**.

Users solve problems in an in-browser editor, run code against custom input,
submit against hidden tests, follow curated learning tracks, join competitions,
and measure progress. Administrators manage problems, contests, and learning
tracks inside the product.

!!! warning "Launch status — 2026-07-13"
    The application-side P0 architecture is implemented and container-verified,
    but MLBoost is **not yet ready for unrestricted production traffic** until
    managed infrastructure, production secrets/configuration, restore/load
    drills, monitoring, and release approvals are completed. See
    [Production readiness](launch/readiness.md).

## Where to begin

| Audience | Start here |
|---|---|
| Product and leadership | [Product overview](product/overview.md) and [July 20 plan](launch/july-20-plan.md) |
| Frontend engineer | [Frontend architecture](architecture/frontend.md) |
| Backend engineer | [Backend architecture](architecture/backend.md) and [API reference](api/reference.md) |
| Platform/SRE | [Deployment](operations/deployment.md), [Observability](operations/observability.md), and [Runbooks](operations/runbooks.md) |
| Security reviewer | [Security](operations/security.md) and [Production readiness](launch/readiness.md) |
| Contributor | [Local development](guides/local-development.md) and [Testing](guides/testing.md) |

## System at a glance

```mermaid
flowchart LR
    U["Learner or admin"] --> F["Next.js frontend + same-origin BFF"]
    F --> A["Private Express API"]
    A --> M[("MongoDB")]
    A --> Q["Durable Mongo evaluation jobs"]
    Q --> W["Separate judge worker"]
    W --> J["Private Judge0 cluster"]
    F -. telemetry .-> S["Sentry / analytics"]
    A -. metrics and logs .-> O["Observability stack — required"]
```

Solid paths describe the implemented, locally container-verified request flow.
Production hosting and observability remain deployment gates.

## Current repositories

| Repository | Purpose | Canonical branch/state |
|---|---|---|
| `frontend` | Next.js product and same-origin BFF | Private; production-hardening branch pending review |
| `backend-api` | Express/Mongo/evaluation worker/Judge0 API | Private; production-hardening branch pending review |
| `documentation` | This source of truth | `main` |

All six organization repositories are private as of 2026-07-13.
