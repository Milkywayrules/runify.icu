---
title: runify.icu — product brief diagrams
type: product-brief-companion
companion: brief-runify-icu.md
status: draft
maintained_by: bmad-product-brief, bmad-spec
---

Skim [`brief-runify-icu.md`](./brief-runify-icu.md) for prose. Diagrams use **flowchart** only (IDE-safe).

## Notification boundary (monorepo module, extractable later)

```mermaid
flowchart TB
  subgraph mono["Turborepo monorepo"]
    subgraph runify["runify apps"]
      API["Catalog / schedule API"]
      WEB["Web app"]
    end
    subgraph notif["Notification module\nseparate app/package"]
      ING["Ingest events"]
      DEL["Deliver in-app / email"]
      INBOX["Per-user inbox + read state"]
    end
  end

  API -->|"scoped domain event"| ING
  WEB -->|"inbox API"| INBOX
  ING --> DEL --> INBOX
```

Same repo today; **MAY** promote notification module to its own repo/deploy later. **MUST NOT** implement delivery/inbox inside catalog or schedule feature code.

## Catalog change to one runner

```mermaid
sequenceDiagram
  participant Admin
  participant runify as runify API
  participant Notif as Notification module
  participant Runner

  Admin->>runify: Update catalog category row
  runify->>runify: Live join updates hub cards
  runify->>Notif: Event scoped to users who saved this row
  Notif->>Runner: In-app notice what changed on their race
```

## Hub visibility (runner schedule entry)

```mermaid
flowchart LR
  A["active\non default hub"]
  R["archived\noff default hub"]
  D["deleted\nhard removed"]

  A -->|archive| R
  R -->|restore| A
  R -->|hard delete only from here| D
```

## Multi-status pattern (entities)

One entity often carries **several orthogonal status fields** (spec names each). v1 examples:

```mermaid
flowchart TB
  E["Catalog category row"]
  E --> P["publish_status\ndraft / published / …"]
  E --> H["hub visibility\nactive / archived / deleted\n(runner schedule entry)"]

  S["Race suggestion"]
  S --> W["workflow_status\nsubmitted / in_review / …"]
```

Payment or checkout statuses are **out of v1** (no organizer checkout on runify).

## Audit fields (all persistent entities)

```mermaid
flowchart LR
  ROW["Every domain row"]
  ROW --> C["created_at / created_by"]
  ROW --> U["updated_at / updated_by"]
  ROW --> D["deleted_at / deleted_by\nwhen soft-delete applies"]
```

Exact column names and soft-delete rules **MUST** be uniform in `bmad-spec` kernel.
