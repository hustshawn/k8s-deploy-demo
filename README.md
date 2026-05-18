# K8s Deploy Demo

GitOps deployment repository for the [k8s-demo](https://github.com/hustshawn/k8s-demo) application.

## Promotion Model

This repo uses **Kargo** with a branch-per-stage promotion pattern:

- `main` branch holds source manifests (`base/` + `stages/`)
- Kargo renders manifests and pushes to `stage/test`, `stage/uat`, `stage/prod` branches
- Argo CD watches each `stage/<name>` branch and syncs to the corresponding namespace

```
main (source of truth)
├── base/               # shared deployment + service + kustomization
└── stages/
    ├── test/           # overlay: namespace kargo-demo-test
    ├── uat/            # overlay: namespace kargo-demo-uat
    └── prod/           # overlay: namespace kargo-demo-prod

stage/test   ← Kargo writes rendered manifests here (auto-promoted)
stage/uat    ← Kargo writes rendered manifests here (manual promotion)
stage/prod   ← Kargo writes rendered manifests here (manual promotion)
```

## Repository Structure

```
k8s-deploy-demo/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
├── stages/
│   ├── test/
│   │   └── kustomization.yaml
│   ├── uat/
│   │   └── kustomization.yaml
│   └── prod/
│       └── kustomization.yaml
└── README.md
```

## Image Tags

CI in [k8s-demo](https://github.com/hustshawn/k8s-demo) pushes timestamp tags (`YYYYMMDD-HHMMSS`) to `ghcr.io/hustshawn/k8s-demo`. Kargo discovers new tags via lexical sorting and promotes them through stages.

## Local Validation

```bash
kubectl kustomize stages/test
kubectl kustomize stages/uat
kubectl kustomize stages/prod
```
