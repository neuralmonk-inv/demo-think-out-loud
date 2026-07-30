# K8sGPT: Main Idea

K8sGPT is an AI-assisted SRE tool that scans Kubernetes clusters, detects common failure modes, and explains them in plain language (optionally with remediation suggestions).

## Core idea

Monitoring shows symptoms; K8sGPT interprets cluster state. Built-in analyzers inspect pods, services, nodes, and related resources, then optionally send anonymized context to an LLM (or run without AI) so on-call engineers get root-cause style narratives instead of raw events alone.

The product's central promise is:

> Diagnose Kubernetes issues faster — AI explanations on top of deterministic cluster analyzers.

## What K8sGPT brings together

- CLI (`k8sgpt analyze`) for ad-hoc troubleshooting
- Optional Operator for continuous in-cluster analysis
- 50+ analyzers for common K8s failure modes
- Multiple AI backends (OpenAI, Azure, Bedrock, Gemini, local Ollama)
- Data anonymization and AI-free analysis modes
- CNCF Sandbox project; MCP server for AI-assistant integration

## Typical workflow

```text
Connect kubectl to a cluster
    ↓
k8sgpt auth (choose AI backend, or skip for rule-only)
    ↓
k8sgpt analyze --explain
    ↓
Review findings; optionally apply suggested fixes carefully
    ↓
Or deploy the Operator for ongoing PolicyReport-style feedback
```

## Why it matters for Interface

When Interface runs on Kubernetes, K8sGPT lowers MTTR for platform and app teams who are still building cluster intuition. It complements OpenTelemetry and dashboards — it does not replace metrics, traces, or alerting.

Use local models (Ollama) if cluster data must not leave the environment.

## Practical starting point

1. Install the CLI and point it at a staging cluster.
2. Run analyzers without `--explain` first to see raw findings.
3. Enable an LLM backend only after reviewing anonymization settings.
4. Integrate `k8sgpt analyze` into incident runbooks, not as a silent auto-fixer in production.
5. Turn on auto-remediation only in non-prod with tight RBAC.
6. Combine with Kyverno reports and OTel signals for a full ops picture.
