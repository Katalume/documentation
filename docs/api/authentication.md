# Authentication

## Token model

| Credential | Transport | Lifetime | Purpose |
|---|---|---:|---|
| Access JWT | Bearer header | 15 minutes | API authorization |
| Refresh JWT | `mlboost_session` httpOnly cookie | 7 days | Session validation and access renewal |

Access JWT claims include user ID, username, and roles. Refresh JWT claims
include user ID.

## Signup

```http
POST /api/auth/signup
Content-Type: application/json

{
  "name": "Ada Learner",
  "email": "ada@example.com",
  "password": "strong-password"
}
```

`username` may be supplied instead of `name`. `role` can only be `User` or
`Organization`; Admin cannot be self-assigned.

Response `201` includes public user data and `accessToken`; the refresh cookie is
set by the response.

## Login

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "ada@example.com",
  "password": "strong-password"
}
```

The `email` field accepts email or username. Response `200` includes user and
access token and sets the refresh cookie.

## Session validation

```http
GET /api/auth/session
Cookie: mlboost_session=<refresh-jwt>
```

Returns `authenticated` and the public user. Responses use `Cache-Control:
no-store`.

## Refresh

```http
POST /api/auth/refresh
Cookie: mlboost_session=<refresh-jwt>
```

Returns a new access token. Current refresh tokens are not rotated or revoked;
this is a P0 production gap.

## Logout

```http
POST /api/auth/logout
```

Clears session cookie variants and returns `204`. It does not invalidate a
copied refresh JWT server-side.

## Cookie topology

Production options depend on the final domain layout:

- Same parent domain (`app.example.com`, `api.example.com`): use an explicit
  shared cookie domain and normally `SameSite=Strict` or `Lax` after testing.
- Cross-site frontend/API: `SameSite=None; Secure` plus CSRF protection.

All production traffic must use HTTPS.

## Target production session model

1. Store refresh sessions server-side by hashed random identifier.
2. Rotate on every refresh.
3. Detect reuse and revoke the session family.
4. Keep access tokens in memory or use a backend-for-frontend cookie session.
5. Support device listing, individual/all-session revocation, admin MFA, email
   verification, password recovery, and password change.
