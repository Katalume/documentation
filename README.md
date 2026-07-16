# Katalume documentation

**Practice machine learning into mastery.**

Katalume is the training ground for machine learning — solve real ML problems
in an in-browser judge, compete in contests, and climb to mastery. LeetCode
rigor meets Kaggle depth.

The name combines **kata**, deliberate practice that forges mastery, with
**lume**, light or illumination—the moment a hard problem clicks.

This repository documents the current product, the frontend and backend
architecture, every API, environment configuration, development workflow,
production operations, security expectations, and the July 20, 2026 launch
plan.

Current public beta: [https://katalume.vercel.app](https://katalume.vercel.app)
with 198 practice problems and pinned in-browser Python execution. Server-side
ranked execution remains intentionally disabled.

## Quick start

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Open `http://127.0.0.1:8000`.

## Source of truth

- Product behavior: public `frontend` repository
- API behavior: public `backend-api` repository
- Practice content: public `ml-problems` repository
- Product and operational documentation: this repository
- Launch gates: [`docs/launch/readiness.md`](docs/launch/readiness.md)
- Deadline plan: [`docs/launch/july-20-plan.md`](docs/launch/july-20-plan.md)

Documentation changes must stay aligned with deployed behavior. When an API,
environment variable, workflow, or user-facing feature changes, update the
corresponding page in the same delivery cycle.

## Validation

```bash
mkdocs build --strict
```

Strict builds run in GitHub Actions on every push to `main` and on pull
requests.
