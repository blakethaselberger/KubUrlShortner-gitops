# KubUrlShortner GitOps Repository

This repository contains Kubernetes manifests for the KubUrlShortner application, organized using Kustomize for environment-specific customizations.

## Directory Structure

```
├── base/                  # Base Kubernetes manifests (shared across all environments)
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   └── kustomization.yaml
└── overlays/              # Environment-specific overlays
    └── dev/              # Development environment
        ├── kustomization.yaml
        └── deployment.yaml
```

## How It Works

- **base/**: Contains the core Kubernetes manifests that are shared across all environments
- **overlays/dev/**: Contains dev-specific customizations (replicas, resource limits, config values)

Kustomize merges the base manifests with the overlay patches to generate the final manifests for each environment.

## Deploying with ArgoCD

This repository is meant to be used with ArgoCD. Create an ArgoCD Application that points to this repository:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: url-shortener
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/YOUR_USERNAME/KubUrlShortner-gitops
    targetRevision: HEAD
    path: overlays/dev
  destination:
    server: https://kubernetes.default.svc
    namespace: url-shortener
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## Making Changes

1. Edit the manifests in `base/` or `overlays/dev/`
2. Push changes to this repository
3. ArgoCD will automatically detect and apply the changes
