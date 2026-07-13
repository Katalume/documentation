# Endpoint reference

Browser paths are same-origin `/api/*`; the BFF forwards them to the private
backend, where paths are relative to `/api` unless noted.

## Authentication and account

| Method | Path | Access | Behavior |
|---|---|---|---|
| `POST` | `/auth/signup` | Public | Create account/Session and cookies |
| `POST` | `/auth/login` | Public | Authenticate email/username and create Session |
| `POST` | `/auth/refresh` | Refresh cookie | Rotate Session and cookies |
| `GET` | `/auth/session` | Refresh cookie | Validate Session and return public user |
| `POST` | `/auth/logout` | Cookie | Revoke current Session and clear cookies |
| `POST` | `/auth/logout-all` | User | Revoke all user Sessions |
| `PUT` | `/auth/password` | User | Change password and revoke other Sessions |
| `GET` | `/auth/account` | User | Download account export |
| `DELETE` | `/auth/account` | User | Password-confirmed cascading deletion |

## Users, progress and profile

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/users/me` | User | Current user without password |
| `GET` | `/users/me/stats` | User | Solved/attempted/submission/difficulty statistics |
| `GET` | `/users/me/progress` | User | Weekly activity, streaks and topics |
| `GET` | `/users` | Admin | Cursor-paginated users, max 100 |
| `GET` | `/users/:id` | Owner/Admin | Read one user |
| `PUT` | `/users/:id` | Owner/Admin | Username/avatar; Admin may set validated roles |
| `DELETE` | `/users/:id` | Admin | Cascading user deletion |
| `GET` | `/profile/me` | User | Profile heatmap/trends/topics |

## Problems and content

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/problems` | Optional user | Tag filter, user status, cursor/limit, max 100 |
| `GET` | `/problems/:slug` | Optional user | Detail/count; editorial only for Admin/accepted solver |
| `POST` | `/problems` | Admin | Create problem and bounded testcases |
| `PUT` | `/problems/:id` | Admin | Optimistic content and atomic-version testcase switch |
| `DELETE` | `/problems/:id` | Admin | Delete problem/testcases |

Problem programs use a deterministic JSON stdin/stdout contract. Testcase
versions publish by a single Problem pointer switch, so workers see either the
old or new set and never a partial edit.

## Execution and submissions

| Method | Path | Access | Behavior |
|---|---|---|---|
| `POST` | `/runner/run` | User/quota | Queue public samples or custom input; return `202` job |
| `GET` | `/runner/jobs/:id` | Owner | Poll run job/result |
| `POST` | `/submissions` | User/quota | Idempotently queue hidden evaluation; return `202` |
| `GET` | `/submissions` | User | Cursor/limit/problem-filtered history |
| `GET` | `/submissions/:id` | Owner | Poll Submission status/verdict |
| `DELETE` | `/submissions/:id` | Owner | Cancel only while atomically queued |

```http
POST /api/submissions
Idempotency-Key: <client-uuid>
Content-Type: application/json

{"problemId":"<id>","code":"<full program>","languageId":71,"contestId":"<optional>"}
```

Statuses include `Queued`, `Processing`, `Cancelled`, all supported Judge0
terminal descriptions, and `Internal Error`. Exhausted jobs enter a durable
dead-letter state while the Submission receives a safe terminal error.

## Contests and leaderboards

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/contests` | Public | Lean list and counts |
| `GET` | `/contests/:id` | Optional user | Problems/count and `isRegistered` |
| `GET` | `/contests/:id/leaderboard` | Public | Score then earliest solve time |
| `POST` | `/contests/:id/register` | User | Atomic pre-end registration |
| `POST/PUT/DELETE` | `/contests[/:id]` | Admin | Audited contest management |
| `GET` | `/leaderboard?limit=20` | Public | Global distinct-problem ranking |

Contest submissions require active time, registration, and contest problem
membership. Accepted first solves update a unique user/contest row exactly once.

## Learning and administration

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/learn/tracks` | Public | Ordered tracks |
| `POST/PUT/DELETE` | `/learn/tracks[/:id]` | Admin | Audited track management |
| `GET` | `/admin/stats` | Admin | Platform counts |
| `GET` | `/admin/audit` | Admin | Cursor-paginated Admin mutation events |

## Health and errors

`GET /health` is liveness. `GET /ready` returns `503` until MongoDB and required
Redis are ready. Both are outside `/api` on the backend and include
`x-request-id`. Every JSON error includes the same request ID. Validation/IDOR
uses `400/401/403/404`, conflicts use `409`, quota uses `429`, asynchronous
creation uses `202`, and unexpected failures return safe `500/502` responses.
