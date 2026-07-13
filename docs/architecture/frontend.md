# Frontend architecture

## Stack

- Next.js 16 App Router, React 19, TypeScript and Tailwind CSS v4
- Monaco editor, `next-themes`, Sentry, Vitest, Testing Library, Playwright
- Node.js 24 standalone non-root production image

## Integration boundary

`src/lib/api.ts` provides live/mock adapters, normalization, retries/timeouts,
polling, analytics, and Admin/product operations. Live browser calls use the
same-origin `/api` route. `src/app/api/[...path]/route.ts` is a BFF that:

- reads the server-only `BACKEND_API_URL`;
- rejects cross-origin state changes;
- forwards only approved headers and cookies;
- preserves cookies, request IDs, locations, content and rate-limit headers;
- returns a safe `502` when the private backend is unavailable.

Live sessions never persist credentials in localStorage. Production responses
contain public user data while authentication remains in Secure, HttpOnly,
SameSite cookies. Expired access cookies are refreshed once with request
deduplication handled by server-side rotation.

## Production guardrails

- Vercel production builds require live mode, mock fallback false, HTTPS
  `BACKEND_API_URL`, and HTTPS canonical site URL.
- CSP, HSTS, frame denial, MIME sniffing, referrer and permissions policies.
- Canonical/Open Graph/Twitter metadata, robots and sitemap.
- Production dependency audit contains zero known vulnerabilities.
- Live contract unit tests cover cookies, runner polling and idempotent submit.
- Eight deterministic browser journeys cover responsive/accessibility/product
  flows without depending on external services.

Mock mode and localStorage are isolated to deterministic demo/test data only.
The approved Blue Eclipse/Ensemble Spark UI is unchanged except removal of the
public organization link required by private-repository policy.
