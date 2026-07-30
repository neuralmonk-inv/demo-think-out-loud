# KServe: Main Idea

KServe is a Kubernetes-native platform for deploying, scaling, and managing predictive and generative AI model inference. It wraps model serving complexity in CRDs so teams ship models without hand-rolling Deployments, autoscaling, and networking for every model.

## Core idea

Inference in production needs more than a container and a port: model loading, GPU scheduling, revision traffic, scale-to-zero or steady capacity, and a standard protocol. KServe provides InferenceService (predictive ML) and LLMInferenceService (generative/LLM) resources, pluggable runtimes (vLLM, TorchServe, etc.), and traffic management.

The product's central promise is:

> Standard Kubernetes APIs for model serving — from classic ML to LLMs — with autoscaling and rollout built in.

## What KServe brings together

- InferenceService for predictive models (TensorFlow, PyTorch, sklearn, XGBoost, ONNX, …)
- LLMInferenceService for generative workloads (OpenAI-compatible APIs, multi-node, caching)
- Pluggable serving runtimes and InferenceGraph for pipelines/ensembles
- Standard and Knative deployment modes (Standard preferred for many LLM production cases)
- Canary / A/B traffic splitting and metric-based autoscaling
- CNCF incubating project; part of the broader Kubeflow / cloud-native ML ecosystem

## Typical workflow

```text
Train or obtain a model; store in S3/GCS/HF/PVC
    ↓
Apply InferenceService or LLMInferenceService YAML
    ↓
KServe creates runtime pods, networking, and probes
    ↓
Clients call standardized HTTP/gRPC or OpenAI-compatible endpoints
    ↓
Roll out new revisions with traffic split; scale on load/GPU metrics
```

## Why it matters for Interface

If Interface products include ML or LLM features that must be self-hosted, KServe is the serving layer on Kubernetes. It is overkill while models are called only via third-party APIs; it becomes important when you need GPU fleets, multi-model routing, or private inference.

OpenShift AI also builds on KServe-style serving — relevant if the enterprise path is OpenShift.

## Practical starting point

1. Decide hosted API vs self-hosted inference for Interface's ML needs.
2. On a GPU-capable cluster, install KServe in Standard mode for a first LLM or sklearn service.
3. Start with one InferenceService from object storage or Hugging Face.
4. Measure cold start, memory, and tokens/sec before enabling complex multi-node setups.
5. Put an AI-aware gateway (kgateway/agentgateway or similar) in front for auth and rate limits.
6. Keep training pipelines separate; KServe focuses on serving, not training orchestration.
