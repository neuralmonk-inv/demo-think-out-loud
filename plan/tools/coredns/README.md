# CoreDNS: Main Idea

CoreDNS is a fast, plugin-based DNS server and the default cluster DNS for Kubernetes. It answers service discovery queries so pods can find each other by name.

## Core idea

Every Service and Pod IP must be discoverable without hardcoding addresses. CoreDNS watches the Kubernetes API and serves DNS names such as `my-svc.my-namespace.svc.cluster.local`, while also forwarding external queries upstream. Configuration lives in a Corefile (usually a ConfigMap).

The product's central promise is:

> Flexible, plugin-chained DNS — the standard service discovery engine inside Kubernetes.

## What CoreDNS brings together

- Default DNS provider in modern Kubernetes (replaced kube-dns)
- Plugin chain: kubernetes, forward, cache, rewrite, prometheus metrics, and more
- CNCF Graduated project
- Works as cluster DNS and as a general-purpose DNS server outside K8s
- Extensible via custom plugins compiled into the binary
- Cloud integrations (Route53, Azure DNS, GCP Cloud DNS plugins)

## Typical workflow

```text
Cluster installs CoreDNS as Deployment + Service (cluster IP for DNS)
    ↓
Corefile ConfigMap defines kubernetes + forward + cache plugins
    ↓
Pods use kubelet-configured nameserver → CoreDNS
    ↓
In-cluster names resolve via kubernetes plugin; others forward upstream
    ↓
Operators tune stub domains, caching, and autoscaling as query load grows
```

## Why it matters for Interface

CoreDNS is infrastructure you inherit with Kubernetes — not something Interface rebuilds. Understanding it matters when debugging "service not found," cross-namespace DNS, custom internal domains, or multi-cluster discovery.

Treat CoreDNS availability as critical path: if DNS fails, most microservice communication fails.

## Practical starting point

1. On any K8s cluster, inspect the `coredns` ConfigMap in `kube-system`.
2. Learn the default search domains and how short names resolve.
3. Add stub domains only when integrating external private zones.
4. Monitor CoreDNS latency/errors via its Prometheus plugin.
5. Scale CoreDNS replicas with cluster size; watch for cache and upstream timeouts.
6. Avoid custom plugins until a clear gap exists — prefer Corefile configuration first.
