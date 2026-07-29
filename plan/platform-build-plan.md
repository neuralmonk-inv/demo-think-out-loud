# Imperyn Platform — Build Plan

## Product thesis

Imperyn should be an opinionated control plane for forward-deployed engineering teams. It should take an enterprise from raw data to a production-ready data/AI application—ingestion, modelling, APIs, governed agents, deployment, and business interfaces—without manually stitching together ten tools.

The platform should integrate proven execution systems rather than recreate them. Its core value is a common resource model, reusable solution blueprints, Git-backed changes, governance, and one operating experience.

## Core abstraction

The primary object is a **Solution Module**, not a ticket.

A solution module contains:

- connectors;
- data products and transformations;
- business entities and relationships;
- APIs and MCP tools;
- agents and durable workflows;
- interfaces;
- infrastructure policies;
- permissions, observability, and audit metadata.

Tickets represent desired changes to a solution:

```text
Intent or ticket
      ↓
Validated change plan
      ↓
Generated Git diff
      ↓
Schema, infrastructure, permission, and cost preview
      ↓
Approval and merge
      ↓
Reconciliation and deployment
      ↓
Runtime health, lineage, and audit trail
```

## Architecture

Separate the system into a proprietary control plane and pluggable execution planes.

```text
┌─────────────────────────────────────────────────────┐
│ Interface                                           │
│ Workspaces · Solutions · Changes · Approvals · Runs │
└─────────────────────────┬───────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────┐
│ Imperyn Control Plane                               │
│ Catalog · Resource API · Reconciler · Policy        │
│ Git sync · Workflow orchestration · Audit           │
└──────┬──────────────┬──────────────┬───────────────┘
       │              │              │
       ▼              ▼              ▼
  Data plane     Runtime plane   Infrastructure plane
  Airbyte/dbt    APIs/MCP        Railway/ECS/K8s
  Warehouses     Agents          Secrets/networking
```

### State ownership

| State | Owner | Examples |
|---|---|---|
| Desired state | Git | manifests, model code, APIs, agent definitions |
| Operational state | Control-plane Postgres | runs, approvals, locks, health, deployments |
| Runtime data | Customer systems | tables, documents, embeddings, logs |

Git is the source of truth for desired state, not live execution state. The control plane continuously reconciles desired and observed state.

## Shared resource model

Initial first-class resources:

- `Organization`, `Workspace`, `Environment`;
- `Solution`, `Connector`, `Dataset`, `Transformation`, `DataProduct`;
- `EntityType`, `RelationshipType`;
- `Service`, `Tool`, `MCPServer`, `Agent`, `Interface`;
- `Deployment`, `Policy`, `SecretReference`.

Example:

```yaml
apiVersion: imperyn.ai/v1
kind: Solution
metadata:
  name: feedback-intelligence
spec:
  environment: production
  sources:
    - ref: zendesk
    - ref: app-reviews
  models:
    - ref: unified-feedback
  services:
    - ref: feedback-api
  agents:
    - ref: feedback-analyst
  interfaces:
    - ref: insight-hub
  policies:
    approvalRequired: true
    piiAccess: restricted
    dataResidency: customer-vpc
```

Every control-plane record should be scoped by organization, workspace, environment, resource ID, and resource version.

## Control-plane components

Start as a modular monolith rather than microservices.

1. **Identity and resource API** — tenancy, RBAC, policy checks, resources, environments, and audit events.
2. **Catalog and dependency graph** — versions, ownership, dependencies, lineage, and runtime associations. Use Postgres first; add a graph database only if proven necessary.
3. **Git service** — GitHub App, repository templates, branches, pull requests, validation, code generation, and drift detection.
4. **Reconciler** — compare desired and observed state and run idempotent provider operations. Expose `Pending`, `Ready`, `Degraded`, and `Failed` conditions.
5. **Durable workflow service** — provisioning, ingestion, transformations, approvals, deployments, rollbacks, and long-running agents.
6. **Provider adapters** — stable interfaces for connectors, warehouses, transformations, deployment targets, secrets, models, and notifications.

## Data and ontology strategy

Do not begin with a universal knowledge graph.

### Version 1: governed data products

Each product has a schema, owner, transformation, freshness target, quality checks, lineage, access policy, and API exposure policy.

### Version 2: typed business entities

Introduce reusable entities such as Customer, Account, Patient, Claim, and Policy. Connect governed data to typed relationships and business actions.

### Version 3: operational ontology

Attach authorization, workflows, actions, agents, and interfaces to entities. Agents should invoke scoped actions such as `Customer.calculateRisk`, not unrestricted SQL.

## Long-running agent runtime

Represent every long-running agent as a durable workflow:

```text
Trigger → Load authorized context → Plan → Invoke tool
        → Persist checkpoint → Wait for event/approval
        → Resume → Validate → Persist result and audit
```

Required primitives:

- retries and idempotency;
- schedules and event triggers;
- human pause/resume and cancellation;
- tool-level authorization;
- time and monetary budgets;
- version-pinned prompts, models, and tools;
- context snapshots, tracing, replay, and evaluations.

Keep workflow state, episodic history, approved knowledge, source-of-truth entity state, and immutable audit records separate.

## Initial stack

- **Frontend:** Next.js, TypeScript, TanStack Query, Monaco, React Flow, and server-sent events.
- **Control plane:** TypeScript/NestJS or Go, PostgreSQL, Temporal, OpenFGA/OPA-style policy, OpenTelemetry, and Redis only for ephemeral needs.
- **Data plane:** Airbyte, dbt Core, object storage, Postgres plus one enterprise warehouse/lakehouse, and OpenLineage-compatible events.
- **Runtime:** FastAPI template, MCP server template, a thin Imperyn agent SDK over Temporal, Docker, and one initial deployment provider.
- **Secrets:** store opaque references to a managed secret store instead of customer credentials in the control-plane database.

## MVP: Feedback Intelligence

The first complete solution should convert customer feedback into a governed production insight application.

### User journey

1. Create a workspace.
2. Connect a warehouse and two feedback sources.
3. Select the Feedback Intelligence blueprint.
4. Map source fields to canonical entities.
5. Preview the generated repository and impact.
6. Run ingestion and transformations.
7. Inspect data quality and lineage.
8. Generate a read-only FastAPI service.
9. Deploy a governed analysis agent.
10. Open the generated Insight Hub.
11. View history, cost, health, and audit events.

### Strict MVP constraints

- GitHub only;
- Airbyte only;
- dbt only;
- Postgres plus one enterprise storage target;
- FastAPI only;
- one durable agent SDK;
- one deployment provider;
- one Insight Hub template;
- no Jira replacement;
- no general drag-and-drop app builder;
- no universal ontology editor;
- no arbitrary Kubernetes provisioning.

## Delivery plan

### Phase 0 — validation (2–3 weeks)

- Interview 5–8 forward-deployed engineers.
- Decompose ten previous implementations.
- Identify repeated versus custom work.
- Define v1 schemas.
- Execute one blueprint manually.
- Verify that it fits at least three real deployments.

### Phase 1 — golden path (6–8 weeks)

- Workspace/project model and basic RBAC.
- GitHub App and template repository.
- Resource catalog and Feedback Intelligence blueprint.
- Deployment workflow, logs, and audit.

Exit criterion: zero-to-running application in less than one day.

### Phase 2 — data platform core (6–8 weeks)

- Connector adapter and dbt execution.
- Schema discovery, quality checks, and lineage.
- Dev/staging/production promotion and rollback.

Exit criterion: promotion is reproducible and auditable.

### Phase 3 — governed agents (6–8 weeks)

- Tools/MCP registry and durable execution.
- Approval steps, context assembly, budgets, and evaluations.
- Agent Hub.

Exit criterion: an agent can pause and safely resume hours or days later.

### Phase 4 — extensibility

- Blueprint and provider SDKs.
- Customer-VPC runner.
- Additional deployment adapters.
- Limited interface composition.
- Marketplace only after internal reuse is proven.

## Enterprise isolation

Support these models progressively:

1. shared control plane and execution plane;
2. shared control plane with dedicated customer execution;
3. shared control plane with a customer-hosted VPC runner.

The preferred enterprise topology is an outbound authenticated channel from a customer VPC runner to the SaaS control plane. Customer data, secrets, and compute remain inside the customer environment.

## Success metrics

- time from credentials to first useful application;
- forward-deployed engineering hours per deployment;
- percentage of implementation reused from modules;
- custom code per customer;
- deployment failure rate and mean time to recovery;
- blueprint upgrade success;
- agent actions requiring correction;
- runtime cost per solution.

The north-star metric is **median engineering time required to deploy a production customer solution**. The target progression is weeks → under three days → hours.

## Major risks

1. **Excessive scope:** enforce one golden path and one provider per category.
2. **Provider coupling:** keep provider-neutral schemas and stable adapter contracts.
3. **Unupgradeable customer forks:** use versioned blueprints, overlays, migrations, and compatibility contracts.
4. **Visual abstraction limits:** keep code and manifests first-class.
5. **Excessive agent authority:** use scoped tools, action-time policy, budgets, approvals, and immutable audit.
6. **Compliance burden:** support customer-hosted data and execution planes.
7. **UI/Git/runtime drift:** explicitly reconcile desired, operational, and observed state.

## Positioning

> A Git-backed control plane where forward-deployed teams assemble proven solution blueprints, connect enterprise systems, review generated changes, and deploy observable data and AI applications with governed agents.

The goal is not to rebuild every underlying platform. The goal is to compress a repeated multi-week enterprise implementation into a safe, reviewable, upgradeable workflow that takes days or hours.
