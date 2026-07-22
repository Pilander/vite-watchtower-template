# AGENTS.md

Instructions for AI coding agents working in this repository. This is the
tool-agnostic companion to `CLAUDE.md` — keep the two in sync. Fill in the
`TODO` sections per project.

---

## Setup

`TODO` — the exact commands an agent should run to get a working environment.

```bash
# TODO: e.g.
cd frontend && npm install
```

## Build & test

`TODO` — how to build, test, and lint. Agents should run these before
considering a task done.

```bash
# TODO: e.g.
cd frontend
npm run build
npm run lint
npm test
```

## Project structure

- `frontend/` — `TODO`
- `backend/` — `TODO`
- `docs/` — project documentation

## Code style

`TODO` — formatting, linting, and naming conventions. Match the surrounding code.

## Do / Don't

**Do**
- `TODO`

**Don't**
- Commit real secrets. Never use `VITE_`-prefixed vars for anything sensitive.
- `TODO`

## Commit & PR conventions

`TODO` — commit message format, branch naming, when to open a PR, required checks.

## Deployment

Push to `main` triggers the GHCR + Watchtower pipeline (see `README.md`).
`TODO` — anything an agent needs to know before pushing.
