---
type: forged-idea
status: hardened
initiative: initiative-runify-icu
---

# runify.icu — forged v1 wedge

## Problem

Runners find races through scattered media, register on official sites, then track many events per year in notes/spreadsheets/manual calendars — reopening each organizer site for details.

## v1 MUST

- Login: email/password + magic link (better-auth).
- RBAC: same user may hold admin + runner roles; admin uses in-app CRUD (no direct DB curation by dev).
- Catalog masterdata: event editions (e.g. JRF 2026) with rich per-category rows (10K, HM, marathon, fees, descriptions, links).
- Runner: browse catalog → add **catalog category row** to **private** schedule (saved details/links/history).
- Runner: **suggest a race** (simple form) → admin verifies → admin publishes to catalog if accepted.
- Calendar/schedule entries **only** reference catalog rows — no custom free-text races.

## v1 MUST NOT

- Strava/Garmin/Coros (or any activity integrations).
- Public profile.
- Groups.
- Share cards.
- Official race registration/checkout on runify.

## Later (explicit)

- Public profile, activity integrations, groups, share cards, full social layer.

## Rejected / deferred from original pitch

- Strava-as-login; personal-only races on calendar; public profile in v1; treating share cards as P1 driver.

## Agent policy

- Confirm with user before assuming product/data rules; ask when build behavior is unclear.

## Next BMad step

**`bmad-product-brief`** — input: this file + memlog; stack remains provisional CANONICAL/stack-proposal until architecture/spec.
