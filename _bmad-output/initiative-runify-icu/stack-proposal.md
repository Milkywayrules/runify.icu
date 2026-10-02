---
type: architecture-proposal
title: runify.icu — stack proposal
initiative: initiative-runify-icu
status: provisional-pre-bmad
extends: CANONICAL.md
superseded_by: bmad-architecture + bmad-spec (see ARTIFACT-LIFECYCLE.md)
bts_version_checked: "3.44.2"
last_updated: "2026-10-03"
---

# Stack proposal — runify.icu

**Non-negotiable rules live in [CANONICAL.md](./CANONICAL.md).** This file adds commands, layout, and deployment detail. If anything here conflicts with CANONICAL, CANONICAL wins.

Verasic infra context: [knowledge-base-of-king-the-user/docs/verasic-labs/tech-stack.md](../../knowledge-base-of-king-the-user/docs/verasic-labs/tech-stack.md) — VPS (`[vts][vps1]`), Coolify (`[vts][cool1]`), Cloudflare DNS (`[vts][cf-dns]`), R2 (`[vts][cf-r2]`).

---

## Locked stack (summary — full MUST/MUST NOT in CANONICAL)

| Layer | Choice |
| --- | --- |
| Monorepo | Better-T-Stack + Turborepo; every app/package uses **`src/`** |
| Package manager | **Bun** for the whole repo |
| Frontend | Next.js **Pages Router** only (`apps/web/src/pages/`) |
| UI | Mantine v7 (app); Tailwind **only** in `apps/web/src/features/share-cards/**` (and similar export/template folders) |
| API | Elysia + **Eden Treaty** — **NOT tRPC** |
| Types / validation | **Zod only** in `packages/validation/`; prefer `z.infer`; `interface` only when Zod cannot fit |
| Data | Drizzle + PostgreSQL |
| Login | better-auth: email/password + magic link |
| Integrations | Strava/Garmin OAuth tokens **after login** — not login providers in v1 |
| Time | dayjs |
| Logs | pino (server) |
| Blobs | Cloudflare R2 |
| Edge | Cloudflare **proxied** DNS on public hosts |
| Deploy | Coolify Docker: **web = Node Next**, **api = Bun Elysia**, Postgres |

---

## Better-T-Stack scaffold (exact flags)

**MUST** pass `--api none` (BTS default tRPC is **forbidden** for this project).

```bash
npx create-better-t-stack@latest . \
  --frontend next \
  --backend elysia \
  --runtime bun \
  --database postgres \
  --orm drizzle \
  --api none \
  --auth better-auth \
  --payments none \
  --addons turborepo \
  --db-setup none \
  --server-deploy docker
```

### Post-scaffold MUST tasks (separate stories)

1. **Delete or stop using** `apps/web/src/app/` — migrate to **`apps/web/src/pages/`** (Pages Router).
2. Wire **Eden Treaty**: export `App` from Elysia; in web, `import { treaty } from '@elysiajs/eden'` + `treaty<App>(apiUrl)`.
3. Create **`apps/server/src/modules/`** and **`apps/web/src/features/`** per feature; add `packages/validation/src/`.
4. Mantine app shell in web; Tailwind config **scoped** to share-card feature paths only.
5. Docker: **web** image uses **Node** to run `next start`; **api** image uses **Bun** to run Elysia.

---

## Eden Treaty (required client)

Elysia documents Eden Treaty as the **recommended** client ([Eden overview](https://elysiajs.com/eden/overview)).

**MUST:**

- Server: single Elysia app type exported as `App`.
- Web: `@elysiajs/eden` Treaty client only for type-safe calls.

**MUST NOT:**

- Add `@trpc/*` packages.
- Use raw `fetch` for internal API calls without sharing types from Elysia (Eden is the default path).

---

## Bun vs Node

| Service | Dev | Staging / prod |
| --- | --- | --- |
| `apps/server` (Elysia) | Bun | Bun |
| `apps/web` (Next.js) | Bun may invoke scripts | **Node** container: `next build` + `next start` |

Reason: Next.js documents full feature support on Node ([deploying](https://nextjs.org/docs/app/building-your-application/deploying)); avoid all-Bun Next production until CI proves otherwise in this repo.

If API ever moves to Node: **pnpm** for that package only; rest stays Bun — **not planned now**.

---

## Mantine + Tailwind (one project)

**MUST:**

- Use Mantine for standard UI (nav, forms, tables, dates via `@mantine/dates` + dayjs).
- Restrict Tailwind entry CSS to share-card/template subfolders so global preflight does not fight Mantine.

**MAY:**

- Use Mantine `classNames` with utility classes where needed ([third-party styles](https://help.mantine.dev/q/third-party-styles)).

**MUST NOT:**

- Make Tailwind the primary styling system for the whole app.
- Add a second CSS framework (Chakra, MUI, etc.).

---

## Activity sync (Strava / Garmin)

| Mode | Requirement |
| --- | --- |
| Automatic | **MUST** — scheduler/worker polls per connected account; **MUST** handle webhooks when configured |
| Manual “Sync now” | **MAY** — optional UX; **MUST NOT** be the only sync mechanism |

Store tokens in Postgres (encrypted); per-integration sync cursor/state in DB.

---

## Target directory layout

```
apps/web/src/
  pages/                    # Pages Router ONLY
  features/
    share-cards/            # Tailwind allowed here
    races/
    calendar/
    ...
apps/server/src/
  modules/
    auth/
    integrations/           # Strava, Garmin — NOT login OAuth
    races/
    sync/                     # background jobs
    ...
packages/db/src/
packages/validation/src/      # all Zod schemas shared by web + server
```

**Module rules:** high cohesion inside each folder; **MUST NOT** import another feature’s internals — use shared `packages/validation` or explicit module public API.

---

## Next BMad step

Run **`bmad-spec`** with **CANONICAL.md** + product brief as input. Every spec section **MUST** link back to CANONICAL for stack/product locks. Then **`bmad-ticket`** with enabler stories for scaffold + Pages migration + Eden wiring first.
