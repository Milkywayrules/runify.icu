---
type: initiative
title: runify.icu
parent: none
---

Verasic Labs product: **runify.icu**.

## BMad-first workflow

Chat = intake. **BMad = system of record.** See [ARTIFACT-LIFECYCLE.md](./ARTIFACT-LIFECYCLE.md).

### BMad outputs (read these)

| Path | Role |
| --- | --- |
| [`forge-runify-icu/`](./forge-runify-icu/) | Forged v1 wedge (input; complete) |
| [`brief-runify-icu/brief-runify-icu.md`](./brief-runify-icu/brief-runify-icu.md) | Product brief (`status: approved`) |
| [`brief-runify-icu/addendum.md`](./brief-runify-icu/addendum.md) | Spec-oriented detail |
| [`brief-runify-icu/diagrams.md`](./brief-runify-icu/diagrams.md) | Mermaid companions (BMad-maintained) |
| [`architecture-runify-icu/architecture-runify-icu.md`](./architecture-runify-icu/architecture-runify-icu.md) | Architecture spine (`status: final`) |

### Archive (stack hold only — not product SoT)

[`archive/pre-bmad/`](./archive/pre-bmad/) — legacy stack/canonical text until **`bmad-spec`** absorbs scaffold detail; **agents MUST NOT** read for product scope (use brief). Delete folder after spec migration per [ARTIFACT-LIFECYCLE.md](./ARTIFACT-LIFECYCLE.md).

### BMad-only agent read order

1. `initiative-runify-icu.md` (this file)
2. `brief-runify-icu/brief-runify-icu.md` + `addendum.md` + `diagrams.md`
3. `forge-runify-icu/` — lineage only when reconciling scope
4. **Not** `archive/pre-bmad/` unless explicitly running **`bmad-architecture`** stack import

### Recommended next step

1. **`bmad-spec`** — kernel from brief + architecture spine (`AD-*`)
2. **`bmad-ticket`** — enablers: BTS scaffold, Pages migration, Eden, notification app shell
4. **`bmad-ticket`** → **`bmad-build`**

Stack is agreed; do not re-litigate unless you explicitly reopen it.
