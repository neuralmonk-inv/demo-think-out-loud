# Backstage: Main Idea

Backstage is an open-source framework for building Internal Developer Portals (IDPs). It gives engineering organizations one place to discover services, docs, ownership, and self-service workflows.

## Core idea

As teams grow, microservices, CI tools, docs, and cloud accounts scatter. Backstage restores a single catalog-backed frontend so product teams can find and create software without hunting across wikis and dashboards.

The product's central promise is:

> One portal for your software catalog, templates, and documentation — so developers ship without losing autonomy.

## What Backstage brings together

- Software Catalog for services, APIs, libraries, websites, and ownership metadata
- Software Templates that scaffold new projects from org-approved golden paths
- TechDocs for docs-as-code rendered next to the components they describe
- A plugin architecture (200+ community plugins) for CI/CD, Kubernetes, security, and cloud tooling
- A CNCF incubating project originally created by Spotify

## Typical workflow

```text
Platform team stands up Backstage
    ↓
Services register in the Software Catalog (YAML + integrations)
    ↓
Developers discover owners, docs, and APIs in one UI
    ↓
New services start from Software Templates (standards baked in)
    ↓
Plugins surface build status, deploys, and ops data per component
```

## Why it matters for Interface

Backstage is not Interface's runtime — it is the developer experience layer on top of whatever Interface runs on (Railway today, Kubernetes later). When service count grows, a catalog and templates reduce tribal knowledge and keep ownership clear.

Treat Backstage as optional until the team feels tool sprawl. Adopt it when you need a shared map of services and a repeatable way to create new ones.

## Practical starting point

1. Stand up a Backstage app (create-app) with auth wired to your identity provider.
2. Define `catalog-info.yaml` for each Interface service and register them.
3. Add one Software Template for a standard service (API + Dockerfile + CI).
4. Enable TechDocs for READMEs already living in Git.
5. Add only the plugins you use daily (GitHub, Kubernetes, CI) — avoid plugin sprawl.
6. Keep Interface's architecture independent of Backstage so the portal is replaceable.
