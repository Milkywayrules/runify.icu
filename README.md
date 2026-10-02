# runify.icu

Runner journey platform (Strava/Garmin, race calendar, groups, public profiles, **share cards**). Web-only, no coaching or official signup in v1.

**Harness:** [BMad Method](https://docs.bmad-method.org/) + Verasic GitHub governance. **BMad-first rule:** `.cursor/rules/runify-bmad-first.mdc` (say **no bmad** to opt out).

## Product docs

**Provisional** (chat capture — delete after BMad replaces them): see [ARTIFACT-LIFECYCLE.md](./_bmad-output/initiative-runify-icu/ARTIFACT-LIFECYCLE.md).

Next: **`bmad-forge-idea`** or **`bmad-brainstorming`** → **`bmad-product-brief`** → **`bmad-spec`** → **`bmad-ticket`**.

## Installed harness

| Piece | Location |
| --- | --- |
| BMad skills + `_bmad/` runtime | `.agents/skills/bmad-*`, `_bmad/` |
| Verasic harness (**cursor** profile) | `.cursor/skills/verasic-*`, `lefthook.yml`, `.github/workflows/ci.yml` |
| Agent GitHub auth template | `.github-agent.local.example` → copy to `.github-agent.local` |

## Quick start in Cursor

1. **`bmad status`** — BMad install should be **current**.
2. **`bash .cursor/skills/verasic-github-governance/scripts/doctor.sh`** — governance **PASS**.
3. Set `GH_TOKEN` in `.github-agent.local`, then **`bash .cursor/skills/verasic-github-cli-init/scripts/check-gh.sh`**.
4. **`bmad-spec`** using the product brief, then **`bmad-ticket`** → **`bmad-build`**.

See [AGENTS.md](./AGENTS.md) for full agent rules.

## Maintenance

```bash
# BMad (skills.sh → .agents/skills/)
npx skills update -p -y

# Verasic (cursor profile → .cursor/skills/; pin bundle after release)
npx skills add Milkywayrules/verasic-skills@v0.2.6 \
  --skill verasic-init --skill verasic-github-governance \
  --skill verasic-github-governance-init --skill verasic-github-cli-init \
  --skill verasic-git-commits-convention --skill verasic-git-commits-audit -y
VERASIC_INIT_BUNDLE_TAG=v0.2.6 bash .cursor/skills/verasic-init/scripts/init.sh --yes --profile cursor

uv run .agents/skills/bmad/scripts/setup.py \
  --project-root "$(pwd)" \
  --skill "$(pwd)/.agents/skills/bmad" \
  --root "$(pwd)/.agents/skills"
```

Prerequisites: Node/npm (Skills CLI), [uv](https://docs.astral.sh/uv/) (BMad Python scripts).
