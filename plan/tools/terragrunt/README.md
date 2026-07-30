# Terragrunt: Main Idea

Terragrunt is a thin wrapper around OpenTofu (and Terraform) that keeps large IaC estates DRY and orchestrates multi-module applies in dependency order.

## Core idea

OpenTofu provisions resources; Terragrunt organizes how you run it across many modules and environments. You put thin `terragrunt.hcl` files in a directory tree; Terragrunt generates backends/providers, injects inputs, and can `run --all` across a DAG of units.

The product's central promise is:

> Scale Terraform/OpenTofu without copy-pasting backend blocks and without manually ordering module applies.

## What Terragrunt brings together

- DRY remote-state and provider configuration via includes
- Dependency blocks that wire module outputs to inputs
- Multi-module orchestration (`terragrunt run --all`)
- Hooks for before/after scripts around plan/apply
- First-class OpenTofu support (current releases default to `tofu`)
- Maintained by Gruntwork; 1.0 stable API commitment (2026)

## Typical workflow

```text
Define shared root.hcl (backend, providers, tofu binary)
    ↓
Each env/module is a thin terragrunt.hcl pointing at a module source
    ↓
terragrunt plan / apply in one unit
    ↓
Or terragrunt run --all across a stack (respecting dependencies)
    ↓
OpenTofu still owns state and resource lifecycle underneath
```

## Why it matters for Interface

Once Interface has more than a handful of OpenTofu roots (network, cluster, data, edge), Terragrunt prevents config drift between environments. It is orchestration glue — not a second IaC language.

Skip it for a single root module; adopt it when copy-paste of backends and env vars starts to hurt.

## Practical starting point

1. Keep reusable modules in a separate repo or `modules/` folder.
2. Create `live/<env>/<component>/terragrunt.hcl` that only sets inputs.
3. Set `terraform_binary = "tofu"` (or `TG_TF_PATH`) in root config.
4. Express dependencies (e.g. cluster depends on VPC) explicitly.
5. Run `terragrunt run --all plan` in CI before merge.
6. Do not put business application deploy logic in Terragrunt — keep that in app CI/GitOps.
