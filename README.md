# MLBoost documentation

The authoritative product and engineering documentation for
[MLBoost](https://github.com/MLBoost2025), an interactive machine-learning and
data-science practice platform.

This repository documents the current product, the frontend and backend
architecture, every API, environment configuration, development workflow,
production operations, security expectations, and the July 20, 2026 launch
plan.

## Quick start

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Open `http://127.0.0.1:8000`.

## Source of truth

- Product behavior: [`frontend`](https://github.com/MLBoost2025/frontend)
- API behavior: [`backend-api`](https://github.com/MLBoost2025/backend-api)
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
