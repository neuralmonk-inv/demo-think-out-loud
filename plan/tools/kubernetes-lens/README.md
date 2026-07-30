# Kubernetes Lens: Main Idea

Lens is a desktop Kubernetes IDE. It gives developers and operators a visual multi-cluster console for workloads, logs, metrics, and terminal access without deploying another dashboard in-cluster.

## Core idea

`kubectl` is powerful but dense. Lens turns kubeconfig contexts into a navigable UI so you can inspect pods, events, and resources across clusters from your workstation.

The product's central promise is:

> Manage Kubernetes visually from your desktop — multi-cluster, with logs and shell access built in.

## What Lens brings together

- Desktop IDE for multiple clusters via kubeconfig
- Workload views, logs, port-forwarding, and in-app terminal
- Metrics when metrics-server (or compatible) is available
- Commercial product by Mirantis (free personal tier + paid Pro/Business)
- Community forks: OpenLens (legacy) and FreeLens (active open-source successor)

## Typical workflow

```text
Install Lens / FreeLens on your workstation
    ↓
Import kubeconfig contexts
    ↓
Browse namespaces, Deployments, Pods, Events
    ↓
Open logs or shell on a pod; port-forward for local debugging
    ↓
Switch contexts to compare staging vs production
```

## Why it matters for Interface

When Interface moves beyond Railway onto Kubernetes, a desktop IDE shortens the learning curve for inspecting clusters. Lens (or FreeLens) is an operator convenience tool — not part of the product architecture.

Prefer FreeLens if you want zero licensing risk for the team; use commercial Lens if you need Pro features and support.

## Practical starting point

1. Choose Lens Desktop or FreeLens based on licensing preference.
2. Point it at a non-production cluster first (kind / minikube / staging).
3. Confirm metrics-server is installed if you want CPU/memory charts.
4. Practice log viewing, describe-equivalent views, and port-forward.
5. Restrict kubeconfig credentials — the IDE is as powerful as the credentials you give it.
6. Pair with CLI tools (kubectl, k9s) rather than treating the IDE as the only interface.
