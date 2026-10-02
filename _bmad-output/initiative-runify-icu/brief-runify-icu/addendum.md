# Addendum — runify.icu product brief

Detail for **spec**, **architecture**, and **UX** without bloating the brief.

## Scope drift (historical)

Early chat provisionals listed integrations + public profile as P1. **Forge + this brief** narrowed v1; archived copies live under [`../archive/pre-bmad/`](../archive/pre-bmad/) for stack reference only — **do not** treat archived product bullets as v1.

## Rejected or deferred (from forge)

- Strava (or any provider) as **login auth**.
- **Free-text personal races** on the schedule (entries **only** reference catalog rows).
- Treating **share cards** or **public profile** as primary drivers for v1.
- **Direct DB curation** by developers as the normal admin workflow.

## Catalog model (conceptual)

- **Event edition** — real-world occurrence (e.g. JRF 2026).
- **Category row** — selectable unit for the runner schedule (e.g. JRF half marathon): fees, distance, description, outbound registration link, edition metadata.

Runners schedule **category rows**, not whole events only, when multiple distances exist.

## Runner schedule — hub visibility (locked)

Separate from catalog **publish/draft** (admin):

| Status | Meaning | Transitions |
| --- | --- | --- |
| **active** | On default My races hub | → **archived** |
| **archived** | Off default hub; user can open archived list | → **active** (restore) or → **deleted** |
| **deleted** | Hard removed | Entry point **only from archived** |

## Editorial / launch

- Initial race count **TBD**; editorial workflow matters more than a launch number.
- Admin review of suggestions may be async from the runner’s perspective.

## User policy (carried from forge)

Agents **MUST** confirm with the product owner before assuming rules that change build behavior; **MUST NOT** reopen v1 scope (integrations, profile, groups, share cards) without explicit approval.

## Notification module (locked)

- **Same monorepo, separate bounded module** — e.g. dedicated `apps/` service and/or `packages/` with **no deep imports** from catalog/schedule into notification internals (and vice versa except public API/events). Exact paths: **`bmad-architecture`**.
- **Own concerns** — ingest events, templates, per-user inbox, read state, channel delivery (in-app first; email optional).
- **runify domain role** — catalog/schedule services emit **scoped events** only; **thin client/adapter** in domain API is OK; **not** inline “send notification” in CRUD handlers beyond that boundary.
- **Promotion path** — design APIs/events so the module **MAY** move to another repo and Coolify deploy later; runify keeps emitting/consuming the same contract.
- **UI** — runify web inbox **MAY** call notification module APIs; not required to share the same process as catalog API.

## Multi-status pattern (Verasic OOT, required in spec)

Do **not** overload one `status` column. Typical dimensions (enable per entity in spec):

| Dimension | Example entities (v1) | Example values |
| --- | --- | --- |
| **publish_status** | catalog edition, category row | draft, published, unpublished |
| **workflow_status** | race suggestion | submitted, in_review, accepted, rejected |
| **hub_visibility** | runner schedule entry | active, archived, deleted |
| **payment_status** | — | **not v1** (no checkout on runify) |

Future entities **MAY** add more dimensions (e.g. step_status) without collapsing into one enum.

## Audit fields (locked)

All persistent domain tables **MUST** include at minimum: `created_at`, `updated_at`, `created_by`, `updated_by` (or equivalent actor id). Soft-deleted rows **MUST** record `deleted_at` / `deleted_by` when soft-delete is used. Spec kernel **MUST** name one convention for the whole monorepo.

## Glossary (until spec kernel)

- **Login auth** — email/password + magic link (better-auth).
- **Integration** — post-login activity OAuth (Strava/Garmin/etc.); **not** v1.
- **Notification module** — bounded notification app/package in this monorepo; extractable to standalone repo/deploy later.
