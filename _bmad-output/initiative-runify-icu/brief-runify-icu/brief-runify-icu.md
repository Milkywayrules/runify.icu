---
title: runify.icu — product brief
type: product-brief
status: approved
initiative: initiative-runify-icu
created: "2026-10-03"
updated: "2026-10-03T02:00+07:00"
inputs:
  - forge-runify-icu/forge-runify-icu.md
  - forge-runify-icu/.memlog.md
stack_reference: architecture-runify-icu/architecture-runify-icu.md (+ archive/pre-bmad/stack-proposal.md for scaffold commands until spec)
supersedes_provisional: retired (see archive/pre-bmad/)
---

# runify.icu — product brief

## Executive summary

**runify.icu** is a **web-only** runner platform whose first release replaces the notes, spreadsheets, and manual calendars that multi-race planners use today. Runners discover races through a **company-curated catalog**, save **specific category rows** (distance, fees, links, edition metadata) into a **private schedule**, and stop reopening every organizer site for the same details each season.

v1 is deliberately narrow: **login**, **RBAC** (runner, race admin, platform super-admin), **catalog masterdata**, **private my-races hub**, **per-user notifications** when catalog data changes on races they saved, and a **suggest → review → publish** path for hidden gems. It does **not** chase integrations, public profiles, groups, or share cards in the first release — those stay on the roadmap after the catalog + calendar wedge proves value.

Tech stack remains **agreed and locked** in [archive/pre-bmad/stack-proposal.md](../archive/pre-bmad/stack-proposal.md) until **`bmad-architecture`**; this brief defines **product** boundaries only.

## The problem

Runners who plan several races per year face two recurring pains:

1. **Scattered discovery** — race information lives across social posts, organizer sites, and word of mouth; finding and comparing editions is luck-heavy and inconsistent.
2. **Fragmented planning** — after registering on official or third-party sites, runners track dates, fees, and links in notes or spreadsheets, then **revisit each organizer site** whenever they need details again.

The cost of the status quo is time, missed context, and no single place that ties **what they signed up for** to **what the platform knows about that race edition**.

## The solution

A responsive web app where:

- **Race admins** maintain rich **event editions** in a catalog (e.g. Jakarta Running Festival 2026) with **per-category rows** (10K, half marathon, marathon, fees, descriptions, outbound links).
- **Anyone** may **browse the catalog** (public read). **Logged-in runners** add **catalog category rows** to a **private** schedule; each entry **shows live catalog fields** on list/card views (date, distance, fees, key copy, outbound registration link) and **MAY** open the full catalog detail page. Runners **MAY** set **status** and **notes** on their entries. Entries **MUST NOT** be free-text custom races detached from catalog masterdata.
- **Runners** may **suggest a race** via a simple form; **admins** verify offline and **publish to the catalog** if accepted.

Authentication uses **email/password and magic link** (login auth per stack). Admin curation happens **in-app**; developers do **not** treat direct database seeding as the primary editorial path.

## What makes this different

- **Catalog-first planning** — the schedule references **masterdata rows**, not ad-hoc text, so details stay consistent and linkable.
- **Curated + crowd-sourced intake** — editorial control stays with admins while runners surface races the catalog might miss.
- **Honest wedge** — v1 optimizes for **multi-race planners**, not activity sync or social showcase features that can follow once the hub is indispensable.

Differentiation at launch is **execution on a tight scope**, not a fabricated technical moat.

## Who this serves

| Actor | Need | Success looks like |
| --- | --- | --- |
| **Runner (default)** | Plan 5+ races/year without spreadsheet chaos | Browses public catalog, saves rows to private schedule, sees live race details + status/notes in one hub |
| **Race admin** | Publish and maintain accurate edition/category data | CRUD catalog entries in-app; review suggestions and publish accepted races |
| **Platform super-admin** | Operate the product (users, roles, system config) | Manages accounts/roles; does not replace in-app catalog editorial workflow for race admins |

Secondary personas (spectators, club organizers, integration-heavy athletes) are **explicitly later** — see Vision.

## Success criteria

Early signals (testable):

- A runner **adds at least one catalog category row** to their private schedule and **returns** to view **live catalog-backed details** (plus their status/notes) without visiting the organizer site for that information.
- A race admin **creates or updates** a catalog edition with **multiple category rows** without database access.
- At least one **runner suggestion** flows through **submit → admin review → catalog publish** (workflow exercised end-to-end).
- **Launch catalog size** is editorially sufficient (exact count **TBD**; not a gate for spec — see addendum).

Business objective for v1: prove the **private my-races hub + curated catalog** replaces manual tracking for the target segment before investing in integrations or social layers.

## Scope

### v1 MUST

- **Login auth** — email/password and magic link (**better-auth**; see stack reference).
- **RBAC** — three roles: **runner**, **race admin**, **platform super-admin**; one human **MAY** hold multiple roles; exact permission matrix **MUST** be fixed in spec.
- **Race catalog** — **public read** for browse/detail; event editions with rich **per-category** rows (distances, fees, descriptions, links). **Login required** only to save to a private schedule and for admin/super-admin tools.
- **Private schedule** — runner **MUST** add **catalog category rows** only; UI **MUST** show **live** catalog field values on hub list/cards (not link-only rows); runner **MAY** add **status** and **notes** per entry. Each saved row **MUST** have a **hub visibility status**: **active** (default list), **archived** (hidden from default hub, restorable), or **deleted** (removed). Runner **MAY** move **active → archived**. **Hard delete** (**archived → deleted**) **MUST** be allowed **only from archived** — not directly from active.
- **Catalog change awareness** — when catalog data changes for a category row on a runner’s schedule, the schedule **updates immediately** (live join). The **catalog API** **MUST** emit a **scoped domain event** consumed by a **notification module** that is a **separate bounded app/package in this monorepo** (own module boundaries, datastore, inbox/read-state — layout in **`bmad-architecture`**). It **MAY** later be **promoted** to its own repo and deployment without rewriting runify domain code. Delivery **MUST** target **only runners who saved that row** (in-app inbox; email **MAY** follow). **MUST NOT** fold delivery/templates/inbox into catalog or schedule feature code, and **MUST NOT** app-wide “catalog updated” broadcasts. See [`diagrams.md`](./diagrams.md).
- **Cross-cutting data rules** — every persistent entity **MUST** include **complete audit fields** (created/updated/by; soft-delete audit when used). Entities **MAY** carry **multiple orthogonal status fields** (e.g. publish vs workflow vs hub visibility); v1 **MUST** at minimum define publish/workflow statuses for catalog and suggestions plus hub visibility on schedule entries — detail in spec kernel.
- **Suggest a race** — simple runner form → admin verification → publish to catalog if accepted.
- **Responsive web** — no native mobile or desktop apps in v1.

### v1 MUST NOT

- **Activity integrations** — Strava, Garmin, Coros, or any similar import.
- **Public profile** — no visitor-facing runner URL in v1.
- **Groups** — shared races, clubs, or group discussion.
- **Share cards** — generated export assets for social.
- **Official registration or checkout** — no payments or signup flows to organizers on runify.
- **Coaching** features.
- **Login via Strava/Garmin** — OAuth for sign-in remains out of scope (integrations, when they ship, attach **after** login).
- **Notification logic inside catalog/schedule modules** — delivery, templates, and inbox **MUST NOT** be implemented inside runify catalog/schedule/web features; **MUST** live in the **monorepo notification module** (extractable later).

### Later (explicit, not v1)

Public profile, activity integrations (read/sync), groups, share cards, and a fuller social layer — **after** catalog + private calendar wedge is validated.

## Vision

If v1 succeeds, runify.icu becomes the **system of record for a runner’s race season**: curated discovery, personal schedule, then optional **activity context**, **shareable identity**, and **community** — in that order. The long-term picture still aligns with a full runner journey platform; the **first release** earns the right to expand by removing spreadsheet friction for serious multi-race planners.

## Product decisions (locked — fusion follow-up)

| Topic | Decision |
| --- | --- |
| Catalog access | Public read; login to save to private schedule |
| Schedule vs catalog edits | **Live** catalog values on entries; changes apply immediately |
| Change awareness | **Notification module** in monorepo (extractable later); per-user delivery for **their** saved rows only |
| Audit + status model | Full audit fields on entities; multiple status dimensions where needed (see addendum) |
| Docs / planning | Prefer **diagrams** in BMad companions ([`diagrams.md`](./diagrams.md)) where they clarify flows |
| Runner fields | **Status** + **notes** on catalog-backed entries |
| Schedule UI | Show actual catalog data on hub cards/list; link to full catalog detail optional |
| Roles | Runner, race admin, platform super-admin |
| Schedule hub visibility | **active** → **archived** → **deleted**; hard delete only from **archived** |

## Open questions (for spec / UX)

- **Required fields** per catalog edition and category row; notification **event payload** (which field changes trigger a notice).
- Notification **package/app path** in monorepo, internal API/event contract, and promotion path to standalone repo (record in **`bmad-architecture`**).
- **Suggestion form** fields, admin review states, runner-visible suggestion status.
- **Initial catalog** editorial bar and launch geography (count TBD).
- **First super-admin bootstrap** (seed/env) without violating “in-app editorial” for catalog content.
- Catalog edition/category **publish** states (draft vs published) for admin — separate from runner hub visibility above.

---

**Next BMad steps:** `bmad-architecture` (record locked stack formally) → `bmad-spec` (kernel absorbs MUST/MUST NOT + glossary) → `bmad-ticket`.
