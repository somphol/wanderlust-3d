# Deployment

Wanderlust 3D is a static site. There is no build command, server runtime, database or environment
variable. Deploy the repository root as-is.

## GitHub Pages (production)

The repository includes `.github/workflows/pages.yml`:

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  deploy:
    environment: github-pages
    runs-on: ubuntu-latest
    steps:
      - checkout
      - actions/configure-pages
      - actions/upload-pages-artifact (path: .)
      - actions/deploy-pages
```

### First deployment

1. Push the repository to `main`.
2. Open **Settings → Pages**.
3. Set **Source** to **GitHub Actions**.
4. Run the workflow (or push again). The first run creates the `github-pages` environment.
5. Wait for the deploy step, then open <https://somphol.github.io/wanderlust-3d/>.

If you prefer the API:

```sh
# Never paste a token into a shell history or a file in this repo.
# Read it from the credential helper into a variable, then:
curl -s -X POST \
  -H "Authorization: Bearer $GITHUB_TOKEN" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/somphol/wanderlust-3d/pages \
  -d '{"build_type":"workflow","source":{"branch":"main","path":"/"}}'
```

Repeat the same call with `PUT` if Pages already exists and you want to change the source. The token
needs `repo` and `workflow` scopes. Do not commit it.

### Subsequent deployments

Any push to `main` triggers the workflow. The site is served from the artifact, not from a `gh-pages`
branch. There is no build output directory.

### Cache behaviour

GitHub Pages caches aggressively at the CDN edge. After a deploy:

- HTML is usually visible within 30–90 seconds.
- A hard refresh (Cmd/Ctrl+Shift+R) is the quickest way to bust a local CSS/JS cache.
- The Pages API returns the current `html_url` and `status`; poll that if a deploy seems stuck.
- If a file name does not change, clients may keep the old version until its cache TTL expires. Rename
  or version the file if you need an immediate, guaranteed invalidation.

### Rollback

Revert the offending commit on `main` and push. The workflow redeploys the previous tree. Because
there is no build artifact to reproduce, a Git revert is a complete rollback.

## Other static hosts

The folder works on any static host:

| Host | Setup |
| --- | --- |
| Netlify | Publish directory `.`; no build command |
| Cloudflare Pages | Framework preset “None”; output `.` |
| Vercel | Framework preset “Other”; output `.` |
| S3 + CloudFront | Upload the repository root; set `index.html` as the index document and `404.html` as the error document |
| nginx/Apache | Serve the directory; set `404.html` for not-found |

For any host, keep the directory structure intact so `docs/…`, `screenshots/…` and `assets/icon.svg`
resolve.

## Custom domain

1. Add a `CNAME` file at the repository root containing the domain (or set it in **Settings → Pages**).
2. Point the DNS record at GitHub Pages:
   - apex: `A` records to GitHub Pages IPs, or `ALIAS`/`ANAME`
   - `www`: `CNAME` to `<user>.github.io`
3. Enable **Enforce HTTPS** once the certificate is issued.
4. Update `canonical`, Open Graph, Twitter, `sitemap.xml` and the JSON-LD `url` to the new domain.

## Local preview

```sh
python3 -m http.server 8080
# or
npm run serve
# or
PORT=9000 npm run serve
```

Open <http://localhost:8080>. Serve over HTTP rather than opening `file://` so `localStorage`, fetch
and relative paths behave consistently.

## Pre-deploy checklist

```sh
npm run verify
npm run verify:images
```

- [ ] Both pass.
- [ ] `screenshots/` matches the current UI (run `npm run screenshots` if the design changed).
- [ ] `AGENTS.md` and `docs/REPRODUCTION.md` reflect any new decisions.
- [ ] `state.json` / `STATE.md` updated if this is a new build.
- [ ] No secrets, tokens, transcript files or personal data in `git status`.
- [ ] The Pages workflow is green.
- [ ] The live URL returns 200 and the Open Graph image resolves.

## Security headers

GitHub Pages does not allow custom response headers. The app itself has no inline user-generated
HTML, no `eval`, no third-party scripts beyond Tailwind's CDN, and no cookies. If a strict CSP is
required, host on a platform that supports headers and add at least:

```text
Content-Security-Policy:
  default-src 'self';
  img-src 'self' https://images.unsplash.com data:;
  style-src 'self' 'unsafe-inline';
  script-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com;
  connect-src 'self';
```

Remove the Tailwind CDN first if you do not need it; that lets you drop `https://cdn.tailwindcss.com`
from `script-src`.
