# Contributing to Katalume documentation

## Principles

- Document behavior that exists; label target-state architecture explicitly.
- Include dates and commit/PR references for time-sensitive status claims.
- Never publish credentials, private infrastructure addresses, user data, or
  unredacted logs.
- Update docs in the same delivery cycle as APIs, environment variables,
  operational procedures, and user workflows.

## Local validation

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs build --strict
```

## Writing style

- Lead with the outcome or decision.
- Use exact endpoint, variable, repository, and branch names.
- Separate current implementation from target state.
- Prefer runnable commands with explicit working directories.
- Use warnings for unsafe or incomplete production behavior.

## Updating API documentation

Verify the actual route file, middleware, controller, response shape, and
frontend consumer. Do not infer contracts from UI labels alone.
