---
type: canonical-reference
title: runify.icu — canonical decisions (read first)
initiative: initiative-runify-icu
status: provisional-pre-bmad
audience: all agents including small/cheap models
last_updated: "2026-10-03"
superseded_by: bmad-spec kernel + AGENTS.md (see ARTIFACT-LIFECYCLE.md)
---

# CANONICAL — runify.icu (provisional)

**Temporary** until `bmad-spec` + `bmad-project-context` absorb these rules.  
**Do not extend this file in chat** — use BMad skills. Delete when [ARTIFACT-LIFECYCLE.md](./ARTIFACT-LIFECYCLE.md) says so.

Until then, agents implementing code **MUST** still follow locks below.  
Extended detail: [product-brief-runify.md](./product-brief-runify.md), [stack-proposal.md](./stack-proposal.md) (also provisional).

---

## 1. Glossary (use these words only)

| Term | Meaning |
| --- | --- |
| **Login auth** | How a human signs into runify.icu (email/password, magic link). Implemented with **better-auth**. |
| **Integration** | Connecting Strava/Garmin (etc.) to **import activity data**. Uses OAuth **tokens stored after login**, not “Sign in with Strava”. |
| **Race catalog** | Company-curated race events on the platform. |
| **User calendar** | Races a user added to their personal schedule. |
| **Share card** | Generated image/asset for social media (priority **#2** after core journey). |
| **Core journey** | Sources → catalog → calendar → profile (priority **#1**). Groups/discussions sit with core unless spec says otherwise. |

---

## 2. Product — LOCKED (v1)

### MUST ship (in order of business priority)

1. **Core journey** — integrations (Strava/Garmin read), race catalog, user calendar, public profile basics.
2. **Share cards** — templates and export (after core is usable).
3. **Groups** — shared races + light discussion (detail in spec/UX; not optional forever, but after core skeleton).

### MUST NOT ship in v1

- Coaching features.
- Official race registration or payments to organizers.
- Native mobile apps or desktop apps (**web responsive only**).
- “Sign in with Strava/Garmin” as **login auth** (integrations only, after login).

### Activity sync behavior (LOCKED)

- **MUST** run **automatic** background sync (scheduled poll + webhooks when available).
- **MAY** offer “Sync now” in UI for recovery/feedback; **MUST NOT** rely on manual sync as the only ingestion path.

---

## 3. Stack — LOCKED

### MUST use

| Area | Choice | Notes |
| --- | --- | --- |
| Monorepo scaffold | Better-T-Stack + Turborepo | See stack-proposal for exact CLI flags. |
| Package manager | **Bun** (`bun install`, `bun run`) | Whole repo unless API moved to Node (then pnpm only for that package — not current plan). |
| Frontend framework | **Next.js Pages Router** | Routes live under `apps/web/src/pages/`. **NOT App Router** (`app/`). |
| UI (main app) | **Mantine v7** | Forms, layout, calendar UI. |
| UI (exceptions) | **Tailwind CSS** | **Only** under share-card / template / export subpaths (see stack-proposal). |
| Backend | **Elysia** on Bun | `apps/server/`. |
| Client → server | **Eden Treaty** (`@elysiajs/eden`) | Server exports Elysia `App` type; web uses `treaty<App>()`. |
| Validation | **Zod only** | Shared schemas in `packages/validation/`. |
| Types | Prefer **`z.infer<typeof Schema>`** or types derived from Zod. Use plain **`interface`** only when Zod cannot model the shape. |
| ORM | **Drizzle** + PostgreSQL | `packages/db/`. |
| Login auth | **better-auth** | Email/password + magic link for v1. |
| Datetime | **dayjs** | Not date-fns as primary. |
| Server logs | **pino** | Not `console.log` in production paths. |
| Object storage | **Cloudflare R2** (S3 API) | Share-card assets, uploads. |
| Public DNS | **Cloudflare proxied** (“orange cloud”) | User-facing hostnames. |
| Deploy target | **Coolify** on Verasic VPS | Docker: web (Node Next), api (Bun Elysia), Postgres. |
| Git / CI | Verasic governance | Feature branches; required check name **`ci`**. |
| Source layout | **`src/`** in every app and package | No feature code at package root outside `src/`. |
| Code organization | Feature modules | `apps/server/src/modules/<name>/`, `apps/web/src/features/<name>/`; **no cross-feature deep imports**. |

### MUST NOT use (forbidden unless CANONICAL is updated)

| Forbidden | Use instead |
| --- | --- |
| **tRPC**, **oRPC** as API layer | Elysia routes + **Eden Treaty** |
| **Prisma**, **TypeORM**, **Mongoose** | **Drizzle** |
| **Yup**, **Joi**, **Valibot**, **AJV** as primary validators | **Zod** |
| **App Router** (`app/` directory) as primary routing | **`pages/`** Pages Router |
| **Laravel**, **Vue**, **Flutter** for this product | Next + Elysia stack above |
| Strava/Garmin OAuth for **login** in v1 | better-auth login; integrations in settings |
| **AWS S3** as primary blob store | **Cloudflare R2** |
| Pushing to **`main`** | Feature branch + PR |

### Runtime — LOCKED (avoid Bun+Next prod surprises)

| Component | Development | Staging / production |
| --- | --- | --- |
| Elysia API | **Bun** | **Bun** |
| Next.js web | **Bun** may run dev scripts | **Node.js** in Docker: `next build` + `next start` |

**MUST NOT** deploy Next.js to production on Bun-only until this repo has a documented, green CI proof and CANONICAL is updated.

---

## 4. Repository paths (after scaffold)

```
apps/web/src/pages/              ← Next.js Pages Router ONLY
apps/web/src/features/<feature>/ ← UI feature code
apps/server/src/modules/<mod>/   ← API feature code
packages/validation/src/         ← Zod schemas (shared)
packages/db/src/                 ← Drizzle schema + migrations
_bmad-output/initiative-runify-icu/  ← specs, tickets, THIS FILE
```

---

## 5. How to write new specs, tickets, and comments (all agents)

When creating or updating **any** planning artifact (`bmad-spec`, `bmad-ticket`, PR descriptions, epic files):

1. **State LOCKED decisions** — copy or link to this file; do not re-decide stack.
2. **Use MUST / MUST NOT / MAY** — avoid “probably”, “maybe use X”, “consider Y”.
3. **Name forbidden alternatives** — e.g. “MUST NOT add tRPC”.
4. **One acceptance criterion per bullet** — testable sentence.
5. **Glossary terms** — use Section 1 words (login auth vs integration).
6. **Open questions** — put under `## TBD` with owner; never smuggle TBD into MUST rules.
7. **File paths** — always absolute from repo root or paths like `apps/web/src/...`.

Cheap/smaller models: if unsure, **stop and read CANONICAL + stack-proposal**; do not invent stack choices.

---

## 6. Document index

| File | Purpose |
| --- | --- |
| **CANONICAL.md** (this file) | Non-negotiable product + stack |
| [product-brief-runify.md](./product-brief-runify.md) | Product narrative and roles |
| [stack-proposal.md](./stack-proposal.md) | Scaffold commands, deployment, module layout |
| [initiative-runify-icu.md](./initiative-runify-icu.md) | Initiative entry point |
| [../../AGENTS.md](../../AGENTS.md) | Repo-wide agent harness rules |
