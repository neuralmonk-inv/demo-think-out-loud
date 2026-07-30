# WebAssembly: Main Idea

WebAssembly (Wasm) is a portable binary format and runtime that can run sandboxed code outside the browser. In cloud-native systems it is emerging as a lightweight alternative to Linux containers for some workloads.

## Core idea

Containers package a full userspace. Wasm packages a small module that starts in milliseconds, uses less memory, and runs under a capability-based sandbox (often via WASI). On Kubernetes, Wasm modules can be scheduled like Pods through containerd shims (runwasi, Spin) and RuntimeClass.

The technology's central promise is:

> Run portable, sandboxed units of code with tiny images and near-instant cold starts — alongside containers, not instead of them.

## What WebAssembly brings together

- Compact modules and sub-millisecond to millisecond cold starts for suitable workloads
- Stronger default isolation via capability-based WASI rather than a full guest OS
- Kubernetes integration via containerd Wasm shims, RuntimeClass, SpinKube, wasmCloud
- Language choice at compile time (Rust, Go, C/C++, and growing ecosystems)
- Not a full replacement for stateful, GPU-heavy, or legacy Linux apps

## Typical workflow

```text
Compile service logic to Wasm (e.g. Spin / WASI target)
    ↓
Package as an OCI artifact and push to a registry
    ↓
Cluster nodes run a Wasm-capable containerd shim
    ↓
Deploy a Pod with RuntimeClass pointing at the Wasm handler
    ↓
Kubernetes schedules it like any other workload
```

## Why it matters for Interface

Wasm is a future runtime option for edge, plugins, or high-density serverless-style APIs — not a near-term requirement while Interface runs primarily on Railway or conventional containers. Keep application logic portable so hot paths could move to Wasm later if cold-start or density becomes a constraint.

## Practical starting point

1. Prototype a small HTTP handler with Spin or Wasmtime locally.
2. Measure image size and cold start vs the equivalent container.
3. On Kubernetes, try SpinKube or runwasi on a non-production node pool.
4. Use Wasm for stateless edge/event handlers; keep databases and GPU/ML on containers.
5. Plan admission policy separately for Wasm OCI media types if you use image signing.
6. Do not rewrite the core Interface stack for Wasm until there is a clear density or latency win.
