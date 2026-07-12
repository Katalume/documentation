# Frontend architecture

## Stack

- Next.js 16 App Router and React 19
- TypeScript and Tailwind CSS v4
- Monaco editor
- `next-themes`
- Sentry
- Vitest/Testing Library and Playwright

## Route map

| Route | Purpose | Access |
|---|---|---|
| `/` | Public landing or authenticated dashboard | Public |
| `/login` | Login/signup | Public; redirects authenticated users |
| `/problems` | Searchable problem catalog | Authenticated |
| `/problems/[slug]` | Solve arena | Authenticated |
| `/competitions` | Competition list | Authenticated |
| `/competitions/[id]` | Detail, registration, standings | Authenticated |
| `/learn` | Learning tracks | Authenticated |
| `/tracks` | Interview tracks and filtered problems | Authenticated |
| `/progress` | Weekly, streak, topic progress | Authenticated |
| `/profile` | Profile and settings | Authenticated |
| `/admin` | Content management | Authenticated Admin |

## Data layer

`src/lib/api.ts` is the single integration layer. It contains:

- Live API fetchers and response normalization
- Mock product data and deterministic mock execution
- Authentication/session persistence
- Retry and timeout behavior
- Submission-history mapping
- Admin CRUD operations

### Backend modes

| Mode | Behavior | Intended use |
|---|---|---|
| `mock` | Never calls backend | Local demo and deterministic tests |
| `live` | Calls backend | Production; fallback must be false |
| `auto` | Calls backend and may fall back | Development only |

## Authentication state

`AuthProvider` restores the session on mount and exposes user, admin state,
login/signup/logout, profile updates, and session refresh.

Live protected routes are guarded by Next.js `src/proxy.ts`, which sends the
request cookie to backend `/auth/session`. Mock mode uses a local demo cookie.

!!! warning
    The live bearer token and serialized session currently persist in
    `localStorage`. Production hardening should move the access token to memory
    or a backend-for-frontend session design.

## Design system

`src/app/globals.css` defines the Blue Eclipse brand scales and shared `.card`,
`.btn-primary`, `.eyebrow`, and `.text-gradient-brand` patterns. The Ensemble
Spark lives in `BrandMark.tsx` and `public/brand/mlboost-mark.svg`.

## Error and telemetry

- App route error boundaries handle render failures.
- Sentry client/server/edge initialization is present but disabled without DSNs.
- Web Vitals are sent to the configured analytics endpoint; events are retained
  locally when no endpoint exists.

## Known architectural work

- Repair the Run endpoint contract.
- Remove live bearer tokens from localStorage.
- Split the 2,000+ line API/mock module into contracts, live client, mock client,
  mappers, and domain services.
- Add generated or runtime-validated API schemas.
- Add frontend coverage thresholds, visual regression, and live contract tests.
- Add CSP/security headers, complete metadata, sitemap, robots, and legal routes.
