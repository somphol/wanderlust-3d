# Security policy

## Scope

Wanderlust 3D is a static, client-side site. It has:

- no server-side code, database, authentication or payment flow
- no cookies, analytics, fingerprinting or third-party tracking
- one storage mechanism: the itinerary in `localStorage`
- external requests to `images.unsplash.com` (photography) and `cdn.tailwindcss.com` (the optional
  Tailwind Play CDN)

## Reporting

If you find a security issue, open a private security advisory on GitHub or contact the repository
owner through their GitHub profile. Please do not open a public issue with exploit details.

Include:

- affected URL and file
- reproduction steps
- impact
- browser/environment
- any suggested fix

## Relevant risks and mitigations

| Risk | Mitigation |
| --- | --- |
| XSS from catalogue strings | All interpolated strings pass through `esc()`; search input is never injected into HTML |
| XSS from URL hash | IDs are matched against the dataset before use; unknown IDs do nothing |
| Third-party script | Tailwind Play CDN is the only script; remove it if a strict CSP is required |
| Supply chain | `puppeteer-core` is a dev dependency only; the deployed site has no npm runtime |
| Data exposure | `localStorage` holds destination IDs and night counts only; no personal data |
| Clickjacking | GitHub Pages cannot set `X-Frame-Options`; use a host with header control if required |
| Dependency drift | Pin the Node engine and regenerate `package-lock.json` deliberately |

## Secrets

No tokens, credentials or private keys belong in this repository. The deployment process reads the
GitHub credential from the local helper; it never writes it to a file or a shell history.

If a secret is committed by accident, revoke it immediately, remove it from history, and rotate.

## Supported versions

The current `main` branch is the supported version. There are no long-lived release branches.
