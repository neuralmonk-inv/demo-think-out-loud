# V1 Implementation Plan

Concrete build plan for Interface V1: GitHub org, repos, workspace model, and what to ship first.

Related: [Platform overview](../platform-overview/README.md).

## Goal

A user can **Start workspace(name)** in the Interface console and get a Ready workspace whose desired state lives in Git. Every change is a PR. Data, modeling, API/agent, infra, and web are folders in one solution repo—not five separate repos yet.

## Decision: GitHub organization

### Naming rules

- Short, lowercase, brand-based
- No spaces; hyphens OK; avoid underscores
- Avoid `.com` / `inc` unless required
- Prefer the product name; engineering slug can differ from marketing domain

### Availability check (2026-07-31)

Checked via `https://github.com/<name>` (404 ≈ likely free, 200 ≈ taken by user or org):

| Name | Status |
|---|---|
| `interfacehq` | Taken (confirmed earlier) |
| `interface-tech` | Taken |
| `interface-ai` | Taken |
| `getinterface` | Taken |
| `interface-labs` | Taken |
| `interface-platform` | Taken |
| `interface-dev` | Taken |
| `withinterface` | Likely free |
| `useinterface` | Likely free |
| `interfaceops` | Likely free |
| `interface-cloud` | Likely free |

Re-check in the GitHub “Create organization” UI before locking in—404 is a signal, not a guarantee.

### Recommendation

| Use case | Pick |
|---|---|
| Public / product-facing org | `withinterface` or `useinterface` |
| Internal engineering + auto `ws-*` repos | `interfaceops` or `interface-cloud` |

**Default for V1:** create org **`withinterface`** (brand-first, likely free).  
Fallback: **`interfaceops`**.

Customer-facing website brand can stay “Interface”; the GitHub slug is only the engineering handle. Org rename is possible later but breaks bookmarks and clone URLs—choose once if you can.

### Org setup checklist

1. Create org (e.g. `withinterface`).
2. Default repository visibility: **private**.
3. Create repo **`solution-template`** and enable **Template repository**.
4. Install a GitHub App later (control plane creates `ws-*` repos from the template).
5. Do not create five repos per workspace in V1.

## Repo model (V1)

### What lives in the org

```text
withinterface/                    # GitHub org
├── solution-template             # master skeleton (Template = on)
└── ws-<customer>-<workspace>     # one monorepo per workspace (auto-created)
```

`ws` means **workspace**. Example: `ws-acme-feedback`.

### What does *not* live here (yet)

- Platform control-plane product code (separate product repo / later)
- Five split repos per workspace (data / modeling / api / infra / web)
- Separate context-only repo

### `solution-template` layout

```text
solution-template/
├── solution.yaml           # desired-state spine; every change PR touches this graph
├── lockfile.yaml           # blueprint version pins (can be minimal in V1)
├── data-pull/              # ingest → Databricks (structured + unstructured)
├── modeling/               # jobs runnable from UI
├── api-agent/              # API, embeddings, agent definitions
├── infra/                  # Terraform for this workspace
├── web/                    # Insight Hub / workspace UI
└── .github/workflows/      # CI per lane + terraform on merge
```

Whiteboard lanes map to **folders**, not separate GitHub repos.

### Change rule

Every UI-driven mutation opens a **PR on that workspace’s `ws-*` repo**.  
Merge updates desired state; the control plane reconciles. No silent writes to prod.

## Tenancy and login (V1)

- Soft isolation: shared GCP project + Databricks; separate catalog/schema/IAM per workspace.
- Humans: SSO into Interface → org → workspace roles.
- Machines: workload identity / service accounts; do not put Databricks tokens in the browser.
- Hard per-customer GCP projects: out of V1.

## Orchestration (V1)

Do **not** start with Temporal.

Use:

- `runs` / job records in the control-plane database
- GCP Cloud Tasks or Pub/Sub + a worker (Cloud Run)

UI returns immediately (`202` + run id); provision and jobs run in the background.

Revisit Temporal Cloud only when agents need multi-hour/day pause–resume.

## GCP slice (V1)

| Piece | Choice |
|---|---|
| Console | Next.js on Cloud Run |
| Control plane API | Modular monolith (NestJS or Go) + Cloud SQL |
| Auth | WorkOS / Auth0 (SSO) |
| AuthZ | OpenFGA (org → workspace → role) |
| Git | GitHub App on `withinterface` (or chosen org) |
| Data | Databricks on GCP + GCS |
| Infra applies | Terraform/OpenTofu via GitHub Actions |
| Secrets | Secret Manager references only |
| App deploy | Cloud Run |

## Build sequence

### Phase 0 — Org and template (this week)

- [ ] Create GitHub org (`withinterface` or fallback).
- [ ] Create `solution-template` with empty folder skeleton + minimal `solution.yaml`.
- [ ] Mark as Template repository.
- [ ] Manually create one test repo from template: `ws-demo-feedback`.
- [ ] Confirm private-by-default and naming convention.

### Phase 1 — Control plane skeleton

- [ ] Console: login + Start workspace(name).
- [ ] API: org / workspace records in Cloud SQL.
- [ ] Basic RBAC (owner / editor / viewer).
- [ ] GitHub App: create `ws-*` from `solution-template`.
- [ ] Seed PR #1 on new workspace (bootstrap).
- [ ] Exit: workspace Ready in under 30 minutes.

### Phase 2 — PR-backed changes

- [ ] UI intent → branch → PR on `ws-*`.
- [ ] CI on PR; merge required for desired-state advance.
- [ ] Reconciler reads `solution.yaml` @ main.
- [ ] Infra folder: Terraform plan/apply via CI.
- [ ] Web folder: deploy to Cloud Run.
- [ ] Exit: no prod change without merge.

### Phase 3 — Data + run from UI

- [ ] `data-pull` path into Databricks.
- [ ] Modeling job trigger from UI → background run + status.
- [ ] Agent/API trigger from UI → audited run.
- [ ] Exit: Feedback Intelligence golden path end-to-end in under one day.

### Phase 4 — Later (explicitly not V1)

- Multi-repo fan-out per workspace
- Context-repo coordination across sibling PRs
- Per-customer GCP project / hard tenancy
- Temporal for durable agents
- Blueprint marketplace

## First blueprint

**Feedback Intelligence** only (see platform overview MVP journey):

1. Create workspace  
2. Connect sources + warehouse  
3. Apply blueprint into `ws-*` via PR  
4. Ingest → model → API → agent → Insight Hub  
5. Audit runs and health in the console  

## Success criteria

1. Start workspace → Ready with a `ws-*` repo from `solution-template`.  
2. Latest change always visible as a PR on that repo.  
3. Modeling and agent runnable from UI with an audit trail.  
4. Soft multi-tenant isolation without per-customer projects.

## Open choices to lock

| Topic | Options | Owner |
|---|---|---|
| Final org slug | `withinterface` / `useinterface` / `interfaceops` / `interface-cloud` | Founders |
| Product repo location | Same org vs separate org for platform code | Eng |
| Auth vendor | WorkOS vs Auth0 | Eng |
| API language | TypeScript/NestJS vs Go | Eng |

## Out of scope reminders

- No Jira replacement  
- No general drag-and-drop app builder  
- No universal ontology editor  
- No arbitrary Kubernetes provisioning  
- No five-repo-per-workspace automation in V1  
