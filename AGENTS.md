# runify.icu — agent harness

it is better to over-commit rather than changing things and lost track of it (like from session-to-session of ai agents works).

## BMad-first (mandatory unless user opts out)

Product/planning/build work **MUST** go through **BMad skills**, not ad-hoc chat execution. Rule: `.cursor/rules/runify-bmad-first.mdc`.  
User may say **no bmad** / **skip bmad** to opt out for that message.

**No heavy assumptions:** confirm with the user before locking behavior that affects product, roles, or data model — all agent tiers (cheap or frontier). Prefer one question over guessing.

Provisional docs in `_bmad-output/initiative-runify-icu/` are **not** the long-term source of truth — see `ARTIFACT-LIFECYCLE.md`. Delete them when BMad artifacts replace them.

## BMad (primary delivery loop)

This repo uses [BMad Method](https://docs.bmad-method.org/) as the AI harness. Skills live under `.agents/skills/`; shared runtime under `_bmad/`; plans and tickets under `_bmad-output/`.

| When you want… | Invoke |
| --- | --- |
| Setup, status, update, active initiative | **`bmad`** (e.g. “bmad status”, “switch initiative”) |
| Small or medium code change | **`bmad-build`** |
| Idea still fuzzy | **`bmad-forge-idea`** or **`bmad-brainstorming`** |
| Research before deciding | **`bmad-deep-recon`** |
| Product → spec → tickets → build | **`bmad-spec`**, **`bmad-ticket`**, then **`bmad-build`** |
| Agent instructions for this repo | **`bmad-project-context`** |
| Team overrides for BMad skills | **`bmad-customize`** |

**Active initiative:** `initiative-runify-icu` (see `_bmad/custom/config.user.toml` — personal, not committed). Artifacts: `_bmad-output/initiative-runify-icu/`.

**Refresh install:** `npx skills update -p -y` then ask **`bmad`** to run setup (or run `uv run .agents/skills/bmad/scripts/setup.py --project-root . --skill .agents/skills/bmad --root .agents/skills --status`).

## Knowledge base (Verasic / King context)

Relative symlink: `knowledge-base-of-king-the-user/` → `../_personal-space`. Index: [knowledge-base-of-king-the-user/AGENTS.md](./knowledge-base-of-king-the-user/AGENTS.md) when the target exists.

Use for company stack, portfolio, and preferences — not for BMad wiring (skills and `_bmad/` live here).

## Canonical product + stack (read before code)

**Single source of truth:** [`_bmad-output/initiative-runify-icu/CANONICAL.md`](_bmad-output/initiative-runify-icu/CANONICAL.md) — locked MUST/MUST NOT, glossary, forbidden libraries.  
Supporting: `product-brief-runify.md`, `stack-proposal.md` in the same folder.

When writing specs, tickets, PRs, or comments: use **MUST / MUST NOT / MAY**, name forbidden alternatives, one testable criterion per bullet (see CANONICAL §5). Do not rely on chat history or implicit defaults.

<!-- bmad:context -->
<!-- Verified 2026-10-03 — CANONICAL.md is locked reference for all agents. Managed by bmad-project-context. -->

## runify.icu

Runner journey web app (see CANONICAL for full locks). **P1:** integrations + race catalog + calendar + profile. **P2:** share cards. **Web only.** Stack: Better-T-Stack, Pages Router, Elysia + Eden Treaty (not tRPC), Zod-only, Drizzle, Mantine + scoped Tailwind, R2, Cloudflare proxy, Coolify.

## Policy

- Do not commit secrets (`.env`, `.github-agent.local`, tokens).
- Do not push to `main`; feature branches + PRs only (see Verasic governance block below).
- Validate boundaries with **Zod** on FE, BE, and shared packages — no alternate schema libraries.
- Keep BMad team customizations in `_bmad/custom/*.toml` (not `*.user.toml`).

## Where things are

- **CANONICAL.md** first, then brief + stack: `_bmad-output/initiative-runify-icu/`
- BMad tickets and specs: same folder as work progresses
- Installed skills: `.agents/skills/` (`bmad-*`, `verasic-*`)

## Running and verifying

- BMad runtime: **`uv`** + `_bmad/scripts/*`; after clone run **`bmad setup`** if `_bmad/` is stale.
- GitHub agent mutations: load env via `.agents/skills/verasic-github-cli-init/scripts/load-gh-env.sh`, verify with `check-gh.sh` (not bare `gh auth status`).
- App commands land after Better-T-Stack scaffold (see `stack-proposal.md`); CI job name **`ci`** until real lint/test wired.

## Conventions that differ from defaults

- Planned multi-session work: **`bmad-spec`** → **`bmad-ticket`** → **`bmad-build`**; small clear fixes: **`bmad-build`** alone.
- Web-only product — no desktop or native mobile apps in scope.

## Known pitfalls

- **`active_initiative`** is personal (`_bmad/custom/config.user.toml`, gitignored).
- Better-T-Stack `frontend: next` may scaffold App Router by default — project requires **`pages/`**; reconcile on first scaffold (see stack proposal).
<!-- /bmad:context -->

<!-- verasic-governance:start -->
## GitHub agent harness

- Load `GH_TOKEN` before any `gh` mutation: `source .agents/skills/verasic-github-cli-init/scripts/load-gh-env.sh`.
- Verify with `bash .agents/skills/verasic-github-cli-init/scripts/check-gh.sh` — never bare `gh auth status` in chat.
- Agents push **feature branches only**; never push to `main`.
- Prefer HTTPS remotes with fine-grained PAT when SSH is unavailable.

## Governance routing

Mutating GitHub operations (repo create, settings, branch protection, CI bootstrap, transfer prep) require the **verasic-github-governance** skill — read `references/governance-protocol.md` and follow `references/factory-protocol.md`.

Soft enforcement (hooks + CI culture + doctor) is the default on private Free plans. OpenTofu hard protection applies only when plan allows and `enable_hard_protection=true`.

Required CI status check name: **`ci`**.

Full spec: install **verasic-github-governance** from [verasic-skills](https://github.com/Milkywayrules/verasic-skills) (`skills/verasic-github-governance/SKILL.md`).
<!-- verasic-governance:end -->
