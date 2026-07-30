# Telepresence: Main Idea

Telepresence connects your local workstation to a remote Kubernetes (or OpenShift) cluster so you can run one service locally while it talks to real cluster dependencies — without rebuild/push/deploy on every change.

## Core idea

Microservice development is slow when every edit requires a container cycle. Telepresence creates a two-way network path (and optional traffic intercept) so your laptop behaves like a pod in the cluster: call cluster DNS/services locally, and optionally replace or intercept a remote service's traffic.

The product's central promise is:

> Code and debug as if your laptop is inside the cluster — skip the container build loop for the service you are changing.

## What Telepresence brings together

- `telepresence connect` for cluster network/DNS access from the laptop
- Intercept / replace / wiretap / ingest modes for different traffic and volume behaviors
- Traffic Manager in-cluster plus traffic-agent (sidecar or node-agent)
- Works with personal intercepts (header-based) so teammates are not blocked
- CNCF Sandbox project (originated at Ambassador)
- Compatible with OpenShift workflows (`oc` as well as `kubectl`)

## Typical workflow

```text
Deploy Traffic Manager once to the cluster
    ↓
telepresence connect (laptop joins cluster network)
    ↓
Run your service locally on a port
    ↓
telepresence intercept/replace the remote service → local process
    ↓
Edit, debug with your IDE; remote callers hit your machine
    ↓
Disconnect when done; remote deployment resumes normally
```

## Why it matters for Interface

Once Interface has multiple services on Kubernetes, Telepresence speeds local iteration against shared staging dependencies (auth, DB proxies, sibling APIs) without running the entire stack on every laptop.

Use carefully on shared clusters — prefer personal intercepts and never leave production intercepts connected.

## Practical starting point

1. Install the CLI; deploy Traffic Manager to a dedicated staging namespace/cluster.
2. Practice `connect` and curling in-cluster services by DNS name.
3. Intercept one Interface service; confirm only your traffic (headers) is routed locally if sharing the env.
4. Mount needed volumes/env from the remote pod when secrets/config matter.
5. Document team etiquette: who may intercept which services, and time limits.
6. Keep production out of scope unless there is a tightly controlled break-glass process.
