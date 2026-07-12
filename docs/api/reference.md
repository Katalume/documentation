# Endpoint reference

All paths below are relative to `/api`.

## Authentication

| Method | Path | Access | Behavior |
|---|---|---|---|
| `POST` | `/auth/signup` | Public | Create User/Organization, set session cookie, return access token |
| `POST` | `/auth/login` | Public | Authenticate by email/username, set cookie, return token |
| `POST` | `/auth/refresh` | Cookie | Return new access token |
| `GET` | `/auth/session` | Cookie | Validate session and return public user |
| `POST` | `/auth/logout` | Public/cookie | Clear session cookies |

## Users and progress

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/users/me` | User | Current user record without password |
| `GET` | `/users/me/stats` | User | Counts, acceptance inputs, per-difficulty totals |
| `GET` | `/users/me/progress` | User | Weekly solved, streaks, topic coverage |
| `GET` | `/users` | Admin | List users |
| `GET` | `/users/:id` | Owner/Admin | Read one user |
| `PUT` | `/users/:id` | Owner/Admin | Update profile; only Admin may change roles |
| `DELETE` | `/users/:id` | Admin | Delete user |
| `GET` | `/profile/me` | User | Rich profile/heatmap/trends/topics |

### Stats response

```json
{
  "totalProblems": 4,
  "solved": 1,
  "attempted": 1,
  "totalSubmissions": 3,
  "acceptedSubmissions": 1,
  "byDifficulty": {
    "Easy": { "solved": 1, "total": 2 },
    "Medium": { "solved": 0, "total": 2 },
    "Hard": { "solved": 0, "total": 0 }
  }
}
```

## Problems

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/problems` | Optional user | List lean problems; `?tags=a,b`; adds user status when authenticated |
| `GET` | `/problems/:slug` | Public | Problem detail and hidden-test count |
| `POST` | `/problems` | Admin | Create problem and optional hidden/full testcases |
| `PUT` | `/problems/:id` | Admin | Update allowed problem fields; slug remains stable |
| `DELETE` | `/problems/:id` | Admin | Delete problem and its Testcases |

!!! warning
    The public detail currently returns editorial content despite the model
    comment that it should unlock after acceptance.

## Execution and submissions

| Method | Path | Access | Behavior |
|---|---|---|---|
| `POST` | `/runner/run` | User | Run code with custom input without saving |
| `POST` | `/submissions` | User | Persist Pending, judge hidden tests synchronously, finalize verdict |
| `GET` | `/submissions` | User | User history; supports `problemId` and `limit` |

### Run request

```json
{
  "code": "print('hello')",
  "languageId": 71,
  "customInput": ""
}
```

### Submit request

```json
{
  "problemId": "<mongo-object-id>",
  "code": "def solve(...): ...",
  "languageId": 71
}
```

!!! danger "Frontend mismatch"
    Frontend live Run currently calls `/submissions/run`; correct backend path is
    `/runner/run`.

The target async API should return `202` with a submission resource, expose a
status endpoint, and make idempotency explicit.

## Contests

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/contests` | Public | Lean contest list with problem/participant counts |
| `GET` | `/contests/:id` | Public | Populated problem detail and participant count |
| `GET` | `/contests/:id/leaderboard` | Public | Stored leaderboard ordered by score/time |
| `POST` | `/contests/:id/register` | User | Register current user |
| `POST` | `/contests` | Admin | Create contest |
| `PUT` | `/contests/:id` | Admin | Update allowed fields |
| `DELETE` | `/contests/:id` | Admin | Delete contest |

Contest submission attribution and active leaderboard updates are not yet
implemented.

## Global leaderboard

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/leaderboard?limit=20` | Public | Rank by distinct accepted problems; max 100 |

## Learning tracks

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/learn/tracks` | Public | Ordered learning tracks |
| `POST` | `/learn/tracks` | Admin | Create track with generated slug |
| `PUT` | `/learn/tracks/:id` | Admin | Update track while preserving slug |
| `DELETE` | `/learn/tracks/:id` | Admin | Delete track |

## Administration

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/admin/stats` | Admin | User/problem/submission counts |

## Health

| Method | Path | Access | Behavior |
|---|---|---|---|
| `GET` | `/health` | Public | Returns `{ "status": "ok" }` outside `/api` |

The current health route is liveness only. Production needs readiness checks for
MongoDB, queue connectivity, and execution capacity without exposing secrets.

## Common status codes

| Code | Meaning |
|---:|---|
| 200 | Successful read/update/run |
| 201 | Created signup/problem/submission/contest/track |
| 204 | Logout completed |
| 400 | Validation or malformed request |
| 401 | Missing/invalid credentials/session |
| 403 | Invalid/expired token or insufficient role |
| 404 | Resource not found |
| 409 | Unique conflict such as duplicate email |
| 429 | Rate limit |
| 500 | Internal or execution integration failure |
