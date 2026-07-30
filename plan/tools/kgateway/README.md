# kgateway: Main Idea

kgateway (formerly Gloo Gateway) is a production-proven control plane that implements the Kubernetes Gateway API with Envoy as the data plane — for ingress, API gateway policies, and advanced traffic management.

## Core idea

Legacy Ingress is limited; the Gateway API is the standard language for cluster edge routing. kgateway translates Gateway, HTTPRoute, and its own policy CRDs into Envoy xDS config, giving you auth, rate limiting, resiliency, and hybrid backends in one Envoy-based gateway.

The product's central promise is:

> Mature Envoy + Gateway API control plane — enterprise-grade ingress and API gateway on Kubernetes.

## What kgateway brings together

- Full Kubernetes Gateway API implementation
- Envoy proxy data plane managed by the kgateway control plane
- Extensions: TrafficPolicies, ListenerPolicies, Backends, and related CRDs
- Authn/authz, rate limiting, and advanced routing in one place
- Hybrid routing to microservices, serverless, and legacy backends
- Evolved from Solo.io Gloo; AI/agent gateway concerns split into the separate agentgateway project
- Alternative in the same space: Envoy Gateway (canonical Envoy project implementation)

## Typical workflow

```text
Install kgateway control plane
    ↓
Create GatewayClass / Gateway (spins up Envoy proxy deployment)
    ↓
Attach HTTPRoutes to services
    ↓
Add TrafficPolicies (auth, timeouts, retries, rate limits)
    ↓
External traffic hits Envoy → routes to cluster backends
```

## Why it matters for Interface

When Interface exposes APIs on Kubernetes, kgateway (or another Gateway API implementation) becomes the edge. Prefer standards-based Gateway API resources so routing config is portable even if the controller changes later.

Railway handles edge networking today; kgateway becomes relevant when you operate your own clusters.

## Practical starting point

1. Compare kgateway vs Envoy Gateway vs your cloud's Gateway API controller for feature needs.
2. Prototype a Gateway + HTTPRoute for one Interface API in staging.
3. Move TLS, timeouts, and retries into Gateway API / policy CRDs — not app code.
4. Add auth and rate limiting at the gateway before growing sidecar meshes.
5. Keep AI/MCP gateway features on agentgateway if that is a separate concern.
6. Load-test Envoy proxies and plan horizontal scaling of gateway replicas.
