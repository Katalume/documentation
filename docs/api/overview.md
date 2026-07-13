# API overview

## Base URL

```text
https://<public-site>/api
```

Local default:

```text
http://localhost:5001/api
```

Health is outside the API prefix at `GET /health`.

## Protocol

- HTTPS JSON API in production
- Request bodies limited to 100 KB
- Browser calls use same-origin BFF routing with `credentials: include`
- Access/refresh use signed Secure/HttpOnly cookies
- The backend origin remains server-only/private

## Response conventions

Successful endpoints return JSON except `POST /auth/logout`, which returns
`204 No Content`. Errors generally return:

```json
{
  "message": "Human-readable error",
  "requestId": "correlation-id"
}
```

Every JSON error and response header carries the same request ID.

## Limits

| Limit | Current value |
|---|---:|
| General API | 300 requests / 15 minutes / IP |
| Authentication | 20 requests / 15 minutes / IP |
| Execution | 15 requests / minute / authenticated user |
| JSON body | 100 KB |
| Source code | 65,536 bytes by default |
| Evaluation testcases | maximum 100 per job |
| Submission history | maximum 100 records per page |
| Leaderboard | maximum 100 records |

Production requires trusted-proxy configuration and shared Redis rate limits.

## Public, authenticated, and admin routes

- **Public:** signup/login/session/logout, problem reads, contest reads,
  leaderboards, learning-track reads
- **Authenticated:** run/submit/history, progress/profile, contest registration,
  user-owned reads/updates
- **Admin:** user listing/deletion, content CRUD, admin stats

See [Authentication](authentication.md) and [Endpoint reference](reference.md).
