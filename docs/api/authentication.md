# Authentication

## Credential model

| Credential | Transport | Lifetime | Server state |
|---|---|---:|---|
| Access JWT | `katalume_access` Secure/HttpOnly cookie | 15 minutes | References Session `sid` |
| Refresh JWT | `katalume_session` Secure/HttpOnly cookie | 7 days | SHA-256 hash, expiry, device metadata, revocation/replacement |

Production browser authentication is cookie-only. Bearer authorization is
disabled by default in production and remains available only for local/test
diagnostics. Production signup/login/refresh responses do not expose access
tokens to JavaScript.

## Signup and login

```http
POST /api/auth/signup
Content-Type: application/json

{"name":"Ada Learner","email":"ada@example.com","password":"strong-password"}
```

`username` may replace `name`; self-service roles are `User` and
`Organization`. Login accepts an email or username in its `email` field. Both
flows create a tracked Session, set both cookies, and return public user data.

## Session and refresh

`GET /api/auth/session` validates refresh signature, token hash, expiry,
revocation and user existence. It uses `Cache-Control: no-store`.

`POST /api/auth/refresh` atomically revokes the current Session, creates its
replacement, rotates both cookies, and returns public user data. Reuse of a
revoked or mismatched refresh token revokes every active Session for that user.

Every authenticated API call validates both the access JWT and referenced live
Session, so logout/password-change revocation takes effect before JWT expiry.

## Account lifecycle

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/auth/logout` | Revoke current Session and clear cookies |
| `POST` | `/auth/logout-all` | Revoke every Session for current user |
| `PUT` | `/auth/password` | Verify old password, change hash, revoke other Sessions |
| `GET` | `/auth/account` | Download account/submission/contest export |
| `DELETE` | `/auth/account` | Password-confirmed cascading deletion |

The same-origin Next.js BFF means backend and public site do not need a shared
cookie parent domain. `COOKIE_DOMAIN` should normally remain blank; production
traffic must use HTTPS and `SameSite=Strict` unless a reviewed topology requires
otherwise.

## Remaining identity gates

Email verification/recovery delivery and mandatory Admin MFA require production
mail/identity decisions and owner enrollment. They remain launch gates rather
than being silently simulated.
