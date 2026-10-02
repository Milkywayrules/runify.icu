---
type: artifact-policy
initiative: initiative-runify-icu
---

# Artifact lifecycle — BMad is the system of record

Chat and manual agent edits are **intake only**. Durable product/planning truth lives in **BMad output folders** under `_bmad-output/initiative-runify-icu/`.

## Provisional files (delete after BMad replaces them)

These were captured before formal BMad workflows. **`status: provisional-pre-bmad`** in frontmatter.

| Provisional file | Replace with (BMad skill) | When to delete provisional |
| --- | --- | --- |
| `product-brief-runify.md` | `bmad-product-brief` → `brief-<slug>/brief-<slug>.md` | After brief is approved / merged into spec input |
| `stack-proposal.md` | `bmad-architecture` + `bmad-spec` companions | After architecture + spec document stack; MUST/MUST NOT live in spec kernel |
| `CANONICAL.md` | `bmad-spec` kernel + `bmad-project-context` → `AGENTS.md` block | After spec locked and AGENTS block refreshed; optional short `DECISIONS.md` only if spec points to it |

**Rule:** When the BMad artifact exists, **delete** the provisional file (or move to `archive/pre-bmad/` in one commit — prefer delete to avoid drift).

## Normal BMad outputs (keep)

| Skill | Typical path pattern |
| --- | --- |
| `bmad-brainstorming` | `brainstorm-<topic>/` |
| `bmad-forge-idea` | `forge-<slug>/` |
| `bmad-product-brief` | `brief-<slug>/` |
| `bmad-prfaq` | `prfaq-<slug>/` |
| `bmad-spec` | `spec-<slug>/` (kernel + companions) |
| `bmad-architecture` | alongside or inside spec folder |
| `bmad-ux` | `DESIGN.md`, `EXPERIENCE.md` per skill |
| `bmad-ticket` | ticket tree / `tickets.toml` per epic |

## Agents

- **MUST NOT** duplicate content across provisional and BMad folders.
- **MUST** update `initiative-runify-icu.md` index when a provisional file is retired.
- **MUST** link spec/tickets to spec kernel for stack/product locks, not to deleted provisionals.
