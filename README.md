# Argocd Multi-environment Example

## Argo Applications Directory Structure

```
.
├── base
│   ├── guestbook-app-blue.yaml
│   ├── guestbook-app-green.yaml
│   └── kustomization.yaml
└── overlays
    └── dev
        ├── guestbook-app-blue.yaml
        ├── guestbook-app-green.yaml
        └── kustomization.yaml
```

## Argo Microservice Directory Structure

```
.
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── svc.yaml
└── overlays
    ├── dev-blue
    │   ├── deployment.yaml
    │   └── kustomization.yaml
    └── dev-green
        ├── deployment.yaml
        └── kustomization.yaml
```

## Terraform Deployment

```
module "eks_blueprints_addon_argocd_apps"  {
  source = "aws-ia/eks-blueprints-addon/aws"
  version = "~> 1.0" #ensure to update this to the latest/desired version

  chart         = "argocd-apps"
  chart_version = "1.4.1"
  repository    = "https://argoproj.github.io/argo-helm"
  description   = "Argo CD Applications"
  namespace     = "argocd"
  set = [
    {
      name  = "applications[0].project"
      value = "default"
    },
    {
      name  = "applications[0].name"
      value = "argocd-apps"
    },
    {
      name  = "applications[0].namespace"
      value = "argocd"
    },
    {
      name  = "applications[0].source.repoURL"
      value = "https://github.com/rohithcj/argocd-multi-env.git"
    },
    {
      name  = "applications[0].source.targetRevision"
      value = "main"
    },
    {
      name  = "applications[0].source.path"
      value = "argo-apps/overlays/dev"
    },
    {
      name  = "applications[0].destination.server"
      value = "https://kubernetes.default.svc"
    }
  ]
}
```