# Interface Planning Documents

This directory is the documentation hub for exploring the Interface platform one component at a time.

## Documents

| Component | Purpose | Status |
|---|---|---|
| [Platform overview](./platform-overview/README.md) | Product thesis, system architecture, MVP, phases, and major risks | Initial draft |
| [V1 implementation](./v1-implementation/README.md) | GitHub org, repo model, GCP slice, and phased build checklist | Initial draft |

## Planned component directories

Future explorations should live in their own directories:

```text
plan/
├── README.md
├── platform-overview/
│   └── README.md
├── v1-implementation/
│   └── README.md
├── control-plane/
├── data-platform/
├── ontology/
├── api-and-mcp-runtime/
├── agent-runtime/
├── infrastructure/
├── interface-builder/
├── security-and-tenancy/
└── observability/
```

Each component directory can contain its own overview, architecture decisions, implementation phases, schemas, open questions, and research notes without turning the main platform document into one large file.
