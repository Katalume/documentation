# Local development

## Prerequisites

- Node.js 24 LTS for the target production toolchain
- npm
- Python 3.13 for documentation
- Docker only when operating Judge0 locally

## Clone layout

```text
katalume/
├── frontend/
├── backend-api/
└── documentation/
```

## Mock frontend

```bash
cd frontend
npm ci
cp .env.example .env.local
npm run dev
```

The default `NEXT_PUBLIC_API_MODE=mock` requires no backend.

## In-memory live stack

Terminal 1:

```bash
cd backend-api
npm ci
npm run dev:memory
```

Terminal 2:

```bash
cd frontend
NEXT_PUBLIC_API_MODE=live \
BACKEND_API_URL=http://localhost:5001/api \
NEXT_PUBLIC_API_FALLBACK_TO_MOCK=false \
npm run dev
```

The development server seeds an admin account and prints development-only
credentials. Never reuse them outside local development.

## Persistent backend

```bash
cd backend-api
npm ci
cp .env.example .env
# Fill Mongo, JWT, CORS, cookie, and Judge0 configuration.
npm run dev
```

## Documentation

```bash
cd documentation
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

## Local ports

| Service | Default |
|---|---:|
| Frontend | 3000 |
| Backend API | 5001 |
| Judge0 | 2358 |
| Documentation | 8000 |

Run `npm run worker` in another backend terminal for live evaluation. Local
MongoDB must be available for durable jobs and Redis is used when `REDIS_URL` is
set. The BFF keeps browser cookies on the frontend origin.
