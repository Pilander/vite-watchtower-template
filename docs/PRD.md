# Product Requirements Document (PRD)

> **This is an example PRD** shipped with the template. Copy it, rename it, and
> replace the example content (a link-shortener app) with your own project's
> details when bootstrapping. Delete this callout when you're done.

---

## 1. Overview

| Field | Value |
|---|---|
| **Product name** | Snippt |
| **Author** | Jane Doe |
| **Status** | Draft |
| **Last updated** | 2026-07-25 |
| **Target release** | v0.1 (MVP) |

**One-liner:** A self-hosted URL shortener that turns long links into short,
shareable slugs and tracks how often each one is clicked.

---

## 2. Problem statement

Team members constantly share long, unwieldy URLs (dashboards, docs, deploy
logs) in chat and tickets. These links are hard to read, easy to mistype, and
impossible to track. We want a lightweight, self-hosted tool — no third-party
service, no per-link cost, full control over data — that produces clean short
links and basic click analytics.

---

## 3. Goals & non-goals

### Goals
- Let a user paste a long URL and get back a short link in one action.
- Redirect short links to their target with minimal latency.
- Show a click count per short link.
- Deploy via the existing GHCR + Watchtower pipeline with zero manual steps.

### Non-goals (for MVP)
- User accounts / authentication.
- Custom domains per link.
- Detailed analytics (geo, referrer, device breakdown).
- Link expiration or editing after creation.

---

## 4. Target users

- **Internal team members** who share links in Slack/tickets and want them tidy.
- **The homeserver operator** who wants a simple service they can host and own.

---

## 5. User stories

- As a user, I can paste a long URL and receive a short link so I can share it.
- As a user, I can click a short link and be redirected to the original URL.
- As a user, I can see how many times a short link has been clicked.
- As an operator, I can deploy the app by pushing to `main`.

---

## 6. Functional requirements

| ID | Requirement | Priority |
|---|---|---|
| FR-1 | Accept a valid `http(s)` URL and generate a unique short slug. | Must |
| FR-2 | Reject invalid or empty URLs with a clear error message. | Must |
| FR-3 | Redirect `GET /:slug` to the stored target URL. | Must |
| FR-4 | Increment and persist a click counter on each redirect. | Must |
| FR-5 | Display the generated short link with a copy-to-clipboard button. | Should |
| FR-6 | List recently created links with their click counts. | Could |

---

## 7. Non-functional requirements

- **Performance:** Redirects resolve in < 100 ms (server-side).
- **Availability:** Single-instance is acceptable for MVP; no HA requirement.
- **Security:** No secrets in `VITE_`-prefixed env vars (they are inlined into
  the public client bundle). Validate/normalize URLs to avoid open-redirect abuse.
- **Data:** Links and counts persist across restarts.

---

## 8. Success metrics

- ≥ 50 short links created in the first month.
- Redirect resolution < 100 ms at p95.
- Zero manual deploy steps after the initial bootstrap.

---

## 9. Milestones

| Milestone | Scope | Target |
|---|---|---|
| M1 — Core | FR-1 → FR-4 (create + redirect + count) | Week 1 |
| M2 — UX | FR-5 (copy button, polished form) | Week 2 |
| M3 — Listing | FR-6 (recent links view) | Week 3 |

---

## 10. Open questions

- Where do links persist for MVP — flat file, SQLite, or an external DB?
- What slug length / alphabet balances brevity vs. collision risk?
- Do we need a delete/cleanup path before launch?

---

## 11. References

- `README.md` — deploy pipeline (GHCR + Watchtower) bootstrap.
- `CLAUDE.md` — repo conventions and guardrails.
