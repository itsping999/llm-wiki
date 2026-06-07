---
title: "Container Orchestration Concept"
slug: "concept-container-orchestration"
type: concept
created: "2026-06-07T09:11:00Z"
updated: "2026-06-07T09:11:00Z"
sources:
  - "https://kubernetes.io/docs/concepts/overview/"
  - "https://docs.docker.com/get-started/docker-overview/"
  - "https://docs.docker.com/compose/"
---

# Container Orchestration Concept

Orchestration answers: where do containers run, how do they scale, and what happens when they fail.

## Single-host vs cluster

| Aspect | Docker Compose | Kubernetes |
| --- | --- | --- |
| Scope | Single host | Multi-node cluster |
| Scaling | `--scale` per service | ReplicaSets, HPA |
| Networking | Bridge network | ClusterIP, NodePort, Ingress |
| Storage | Volumes, bind mounts | PV, PVC, StorageClasses |
| Secrets | `.env` files, Docker secrets | Kubernetes Secrets, external vaults |

## Core Kubernetes objects

- **Pod**: smallest deployable unit (1+ containers sharing network/storage).
- **Deployment**: manages ReplicaSets for rolling updates and rollbacks.
- **Service**: stable network endpoint for a set of pods.
- **ConfigMap/Secret**: externalized configuration and sensitive data.
- **Ingress**: HTTP routing from external traffic to services.

## Workflow: from Compose to Kubernetes

1. Start with Docker Compose for local development.
2. Define container images with Dockerfiles and Compose.
3. Translate to Kubernetes manifests (or Helm charts) for production.
4. Use `kubectl` for day-2 operations: rollout, scale, debug.

## Useful official references

- Kubernetes concepts: [https://kubernetes.io/docs/concepts/overview/](https://kubernetes.io/docs/concepts/overview/)
- Docker overview: [https://docs.docker.com/get-started/docker-overview/](https://docs.docker.com/get-started/docker-overview/)
- Docker Compose: [https://docs.docker.com/compose/](https://docs.docker.com/compose/)

## Extend this page next

- Add Helm, Kustomize, and GitOps patterns.
- Add service mesh and observability (Envoy, Istio, Prometheus).
- Add CI/CD integration: building, scanning, and deploying container images.
