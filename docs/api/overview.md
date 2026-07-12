# API overview

## Base URL

```text
https://<api-host>/api
```

Local default:

```text
http://localhost:5001/api
```

Health is outside the API prefix at `GET /health`.

## Protocol

- HTTPS JSON API in production
- Request bodies limited to 100 KB
- Authenticated routes use `Authorization: Bearer <access-token>`
- Refresh/session uses the signed httpOnly `mlboost_session` cookie
- Browser calls use `credentials: include`

## Response conventions

Successful endpoints return JSON except `POST /auth/logout`, which returns
`204 No Content`. Errors generally return:

```json
{
  "message": "Human-readable error"
}
```

The backend should standardize this into a versioned error schema with error
codes, request IDs, and safe details before public launch.

## Limits

| Limit | Current value |
|---|---:|
| General API | 300 requests / 15 minutes / IP |
| Authentication | 20 requests / 15 minutes / IP |
| Execution | 15 requests / minute / IP |
| JSON body | 100 KB |
| Source code | 65,536 bytes by default |
| Submission history | maximum 100 records |
| Leaderboard | maximum 100 records |

Production must configure trusted proxy behavior and use a shared rate-limit
store across API instances.

## Public, authenticated, and admin routes

- **Public:** signup/login/session/logout, problem reads, contest reads,
  leaderboards, learning-track reads
- **Authenticated:** run/submit/history, progress/profile, contest registration,
  user-owned reads/updates
- **Admin:** user listing/deletion, content CRUD, admin stats

See [Authentication](authentication.md) and [Endpoint reference](reference.md).
