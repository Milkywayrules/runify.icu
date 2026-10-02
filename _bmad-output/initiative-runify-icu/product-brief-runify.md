---
type: product-brief
title: runify.icu — runner journey platform
initiative: initiative-runify-icu
status: provisional-pre-bmad
extends: CANONICAL.md
superseded_by: bmad-product-brief output (see ARTIFACT-LIFECYCLE.md)
---

# runify.icu — product brief (provisional)

**Chat capture — not BMad official.** Replace with **`bmad-product-brief`** (or absorb via **`bmad-spec`** after forge/brainstorm).

**Until replaced:** [CANONICAL.md](./CANONICAL.md), [stack-proposal.md](./stack-proposal.md).

## One-liner

Web platform: runners connect **integrations** (Strava/Garmin), browse **race catalog**, build a **user calendar**, optional **groups**, **public profile**, and **share cards** (priority #2).

## Priority (business — do not reorder without human approval)

| Priority | Scope |
| --- | --- |
| **P1** | Integrations (read) + race catalog + user calendar + public profile basics |
| **P2** | Share card generation and export |
| **P1.5** | Groups + shared races + light discussion (after P1 skeleton; exact UX in spec) |

## MUST deliver (v1)

1. **Integrations** — User logged in via **login auth** can connect Strava and/or Garmin; activities sync **automatically** (see CANONICAL).
2. **Race catalog** — Platform lists races; **race admin** role can create/edit/publish.
3. **User calendar** — User adds catalog races to personal schedule; calendar view.
4. **Public profile** — URL others can visit: name, avatar, bio, upcoming races, pinned races (social links TBD in spec).
5. **Share cards** — User generates at least one exportable card from run/race context (templates TBD in spec).
6. **Basics** — Settings, audit/activity log patterns as features land.

## MUST NOT deliver (v1)

- Coaching.
- Official race signup / payments to organizers.
- Native mobile or desktop apps (responsive web only).
- Strava/Garmin as **login auth** (they are **integrations** only).

## Roles

| Role | MUST be able to |
| --- | --- |
| Runner (default) | Login; connect integrations; use calendar; join/create groups; edit own profile; create share cards |
| Race admin | CRUD **race catalog** entries |
| Super admin | Users, roles, system config (detail in spec) |

## Glossary (same as CANONICAL)

Use **login auth** vs **integration** exactly as defined in CANONICAL.md — do not conflate OAuth for data with OAuth for sign-in.

## TBD (spec / UX — not stack)

- Group privacy (public / invite / link).
- Discussion shape (feed vs threads).
- Profile visibility defaults.
- Required race fields (distance, date, location, etc.).
- Share card formats (PNG, story ratio, OG).

## Success signals (early)

- User connects an integration and sees synced activity context without clicking sync every time.
- User adds a catalog race to calendar; it appears on public profile.
- User exports a share card they would post externally.
