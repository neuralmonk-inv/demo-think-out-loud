# OpenTofu: Main Idea

OpenTofu is an open-source Infrastructure as Code (IaC) engine — a community fork of Terraform — that provisions and manages cloud and on-prem infrastructure with declarative HCL.

## Core idea

You describe desired infrastructure in `.tf` files; OpenTofu plans the diff against state and applies changes safely. It forked from Terraform 1.5 after HashiCorp's BSL license change and remains under MPL 2.0, now a CNCF Sandbox project.

The product's central promise is:

> Predictably provision infrastructure with open-source HCL — drop-in compatible with Terraform workflows, without BSL license constraints.

## What OpenTofu brings together

- Declarative HCL and the familiar plan/apply workflow
- Broad provider ecosystem (mirrors the Terraform provider registry)
- Compatible state format with Terraform ~1.5 baseline
- Differentiating features such as native state encryption and provider `for_each`
- Vendor-neutral governance under the Linux Foundation / CNCF

## Typical workflow

```text
Write modules and root configs in HCL
    ↓
tofu init (providers + backend)
    ↓
tofu plan (preview changes)
    ↓
tofu apply (update real resources + state)
    ↓
Repeat per environment; optionally wrap with Terragrunt
```

## Why it matters for Interface

When Interface outgrows Railway's UI for infra (VPCs, managed K8s, IAM, databases), OpenTofu is the portable IaC default. Prefer it over Terraform CLI for greenfield OSS-first stacks unless you are locked into HCP Terraform / enterprise HashiCorp products.

Keep Interface app config out of raw cloud consoles so environments stay reproducible.

## Practical starting point

1. Install the `tofu` CLI and pin a version in CI.
2. Start with remote state (S3/GCS + locking) before any shared apply.
3. Module-ize VPC, cluster, and datastore separately.
4. Use workspaces or separate roots per env (dev/staging/prod).
5. Add policy checks (OPA/Kyverno for K8s; Sentinel alternatives or plain CI checks for cloud).
6. Pair with Terragrunt when DRY backends and multi-module orchestration become painful.
