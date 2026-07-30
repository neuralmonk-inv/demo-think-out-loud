# Kyverno: Main Idea

Kyverno is a Kubernetes-native policy engine. You write policies as YAML (and CEL) to validate, mutate, generate, and clean up cluster resources — without learning a separate policy language like Rego.

## Core idea

Admission control and ongoing governance should feel like Kubernetes itself. Kyverno runs as an admission controller (and CLI/scanner) so platform teams can enforce standards — image provenance, required labels, resource limits, network defaults — as code.

The product's central promise is:

> Policy as Code for Kubernetes in familiar YAML/CEL — validate, mutate, generate, and clean up at scale.

## What Kyverno brings together

- Validate / Mutate / Generate / Cleanup (and image verification) policy types
- Policies as Kubernetes resources, GitOps-friendly
- CLI for unit tests (`kyverno test`) and CI dry-runs
- Policy reporting aligned with Kubernetes policy reporting patterns
- CNCF Graduated project (2026); CEL-based policy types becoming the modern path
- Common alternative comparison: OPA/Gatekeeper (more general Rego, steeper learning curve)

## Typical workflow

```text
Author policies in Git (require labels, block :latest, inject sidecars, etc.)
    ↓
kyverno test in CI against sample resources
    ↓
Deploy Kyverno + policies to the cluster (GitOps)
    ↓
Admission webhook enforces on create/update
    ↓
Reports surface violations for existing workloads
```

## Why it matters for Interface

As soon as Interface shares a Kubernetes cluster across services or tenants, Kyverno is the guardrail layer that keeps self-service safe. It encodes platform standards so Backstage templates and CI do not become the only line of defense.

## Practical starting point

1. Install Kyverno on a non-production cluster.
2. Start with Audit mode policies (report, do not block) for required labels and resource requests.
3. Add image verification / ban of untrusted registries before Enforce mode.
4. Keep policies in Git next to cluster config; test with the Kyverno CLI.
5. Prefer new CEL-based policy APIs for greenfield work (legacy ClusterPolicy path is sunsetting).
6. Pair with OpenTofu/Terragrunt for cluster bootstrap and GitOps for ongoing policy delivery.
