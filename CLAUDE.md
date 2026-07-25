# CLAUDE.md

Guidance for Claude Code (and other AI agents) working in this repository.
Fill in the `TODO` sections when bootstrapping a new project from this template.

---

## Project overview

- **Name:** `TODO` — the project slug (lowercase, no spaces). This is the same value
  that replaces `PROJECT_NAME` in `.github/workflows/deploy.yml` and `docker-compose.yml`.
- **What it does:** `TODO` — one or two sentences on the app's purpose.
- **Public URL:** `TODO` — e.g. `https://<slug>.pilander.com`
- **Status:** `TODO` — e.g. prototype / active / maintenance

## Repository layout

| Path | Purpose |
|---|---|
| `frontend/` | `TODO` — Vite app (source, `package.json`, builds to `dist/`) |
| `backend/` | `TODO` — API / server code (or "unused" if this is a static-only app) |
| `docs/` | Project documentation, notes, decisions |
| `Dockerfile` | Multi-stage build → nginx serves the static `dist/` |
| `nginx.conf` | SPA fallback + asset caching |
| `docker-compose.yml` | Server deploy manifest (Traefik + Watchtower labels) |
| `.github/workflows/deploy.yml` | CI: build → push to GHCR → trigger Watchtower |

> If the frontend/backend live in subdirectories, set `BUILD_CONTEXT` in the
> workflow and make sure the `Dockerfile` paths match.

## Tech stack

- **Frontend:** `TODO` — e.g. Vite + React + TypeScript
- **Backend:** `TODO` — e.g. Node/Express, or "none"
- **Package manager:** `TODO` — e.g. npm
- **Deploy:** GHCR image + Watchtower on the Pilander homeserver (see `README.md`)

## Commands

`TODO` — fill in once the app exists.

```bash
# frontend
cd frontend
npm install
npm run dev        # local dev server
npm run build      # production build → dist/
npm run lint       # TODO
npm test           # TODO

# backend
# TODO
```

## Conventions & guardrails

- `TODO` — code style, naming, formatting rules the agent must follow.
- **Never** put real secrets in `VITE_`-prefixed env vars — Vite inlines them into
  the public client bundle at build time.
- `TODO` — anything else agents should know (branch naming, commit style, etc.).

## Deployment notes

- Push to `main` → Actions builds & pushes `ghcr.io/pilander<slug>:latest` →
  Watchtower redeploys. See `README.md` for the full one-time bootstrap.
- `TODO` — project-specific deploy quirks, if any.
