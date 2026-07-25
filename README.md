# vite-watchtower-template

A GitHub **template repository** for deploying a static/SPA web app to the
Pilander homeserver via **GHCR + Watchtower**. Push to `main` → GitHub Actions
builds a Docker image, pushes it to GHCR, and pings Watchtower, which
auto-redeploys the container.

```
push to main → Actions builds image → ghcr.io/pilander/<project> → Watchtower redeploys
```

---

## What's in here

| File | Purpose |
|---|---|
| `Dockerfile` | Multi-stage: `node:22-alpine` builds `dist/`, `nginx:alpine` serves it |
| `nginx.conf` | SPA fallback (`try_files … /index.html`) + asset caching |
| `.dockerignore` | Keeps `node_modules`/`dist` out of the build context |
| `.github/workflows/deploy.yml` | Build → push to GHCR → trigger Watchtower |
| `docker-compose.yml` | Server deploy manifest (Traefik + Watchtower labels) |

> **This template assumes a Vite-style app** whose `npm run build` outputs to
> `dist/`. For other build tools, adjust the `Dockerfile` build stage and the
> nginx `root` accordingly.

---

## New project bootstrap (do this once per repo)

### 1. Create the repo from this template
GitHub → **New repository** → **Repository template** → `vite-watchtower-template`.
Create it under the **Pilander** org. **Public** is recommended (see
[Public vs private](#public-vs-private-packages) below).

### 2. Replace the `PROJECT_NAME` placeholder
Two files reference `PROJECT_NAME` — replace it with your project slug
(lowercase, no spaces), e.g. `myapp`:

- `.github/workflows/deploy.yml` → `env.IMAGE` (and `BUILD_CONTEXT` if your app
  is in a subdirectory like `frontend/` — default is repo root `.`)
- `docker-compose.yml` → service name, `container_name`, image, and all four
  Traefik labels

Quick find-replace from the repo root:
```bash
grep -rl PROJECT_NAME . | xargs sed -i '' 's/PROJECT_NAME/myapp/g'   # macOS
# grep -rl PROJECT_NAME . | xargs sed -i 's/PROJECT_NAME/myapp/g'    # Linux
```

### 3. Add your app's source
Drop in your Vite app (`package.json`, `src/`, etc.). Confirm `npm run build`
produces `dist/`. If the app lives in a subdir, set `BUILD_CONTEXT` in the
workflow to that subdir and move the `Dockerfile`/`nginx.conf`/`.dockerignore`
into it.

### 4. Secrets
The workflow needs `WATCHTOWER_URL` and `WATCHTOWER_TOKEN`:

- **Public repo** → inherited from **Pilander org secrets** automatically. ✅
  Nothing to do.
- **Private repo** → org secrets do **not** reach private repos on the Free
  plan. Set them per-repo:
  ```bash
  gh secret set WATCHTOWER_URL   --repo Pilander/<repo> --body "https://watchtower.pilander.com"
  gh secret set WATCHTOWER_TOKEN --repo Pilander/<repo> --body "<token>"
  ```

### 5. Push
```bash
git add -A && git commit -m "Initial commit" && git push origin main
```
Watch the Action: it builds, pushes `ghcr.io/pilander/<project>:latest`, and
triggers Watchtower.

### 6. Deploy on the server (first time only)
Copy `docker-compose.yml` onto the homeserver (via Dockge or manually) and bring
it up once:
```bash
docker compose up -d
```
Watchtower keeps it updated on every subsequent push — you never run this again.

### 7. Cloudflare tunnel route
Add a public hostname in the Cloudflare tunnel (Zero Trust → Networks → Tunnels →
your tunnel → **Routes** → Add route → **Published application**):

- **Subdomain:** `myapp` (or blank for the apex — see gotcha below)
- **Domain:** `pilander.com`
- **Service:** `http://traefik:80`

---

## Public vs private packages

**Recommendation: keep the GHCR package public.** A compiled static-site image
contains only the assets any visitor already downloads — there is no secret in
it. Public packages pull with zero credentials, which means Watchtower, Dockge's
Update button, and manual `docker pull` all "just work."

Making a package private forces you to wire GHCR credentials into *every* tool
that pulls (Watchtower's mounted `config.json`, Dockge's container, your shell) —
three separate credential stores, for no security benefit on a static site.

> ⚠️ **Never** put a real secret in a `VITE_`-prefixed env var — Vite inlines
> those into the public client bundle at build time regardless of package
> visibility.

To set a package public: **Pilander → Packages → `<project>` → Package
settings → Change visibility → Public.**

---

## Gotchas (learned the hard way)

- **Apex vs wildcard.** A Cloudflare `*.pilander.com` route matches
  `foo.pilander.com` but **not** the bare apex `pilander.com`. Apex projects need
  their own route (blank subdomain).
- **SPA fallback is mandatory** for client-side routing. It's in `nginx.conf` —
  don't remove the `try_files` line, or direct loads/refreshes of sub-routes 404.
- **Traefik `web`/`tls=false`** is HTTP-only behind Cloudflare (which terminates
  TLS). That's fine for this setup; don't add `tls=true` unless you're doing
  origin TLS.
- **`container_name` and the Traefik router name must be unique** across all
  stacks on the server — that's what `PROJECT_NAME` guarantees.

---

## The pipeline in one sentence

Push to `main` → Actions builds & pushes a public GHCR image → Watchtower on the
homeserver pulls it and recreates the container → Traefik routes
`<project>.pilander.com` to it → Cloudflare tunnels the public hostname in.
