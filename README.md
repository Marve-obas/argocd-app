# Argo CD Application

This repository contains Kubernetes files for deploying `myapp-argocd` with
Argo CD.

## What is in this repository?

```text
.
├── application.yaml
└── dev/
    ├── deployment.yaml
    └── service.yaml
```

`dev/deployment.yaml` creates the Deployment named `myapp-argocd`. It runs two
copies of the application, each using the `nanajanashia/argocd-app:1.2` image
on port `8080`.

`dev/service.yaml` creates the Service named `myapp-service`. It sends traffic
on port `8080` to Pods with the label `app: myapp`.

The Deployment gives its Pods the same `app: myapp` label, so the Service can
find and send traffic to them. The Deployment name and the label do not need to
be identical.

`application.yaml` creates the Argo CD Application named
`myapp-argo-application`. Argo CD reads the `dev` folder in this GitHub
repository and deploys it to the `s12kunballi` namespace.

## Argo CD settings

Argo CD watches this GitHub repository and branch:

```text
https://github.com/Marve-obas/argocd-app.git
feature/branch-s12kunball-argocd-deploment
```

Automatic sync is configured with:

- `CreateNamespace=true`: create the `s12kunballi` namespace if it does not exist.
- `enabled: true`: turn automatic sync on.
- `selfHeal: true`: restore the Kubernetes configuration if someone changes it manually.
- `prune: true`: delete Kubernetes objects removed from the Git configuration.

Be careful with `prune: true`: if a resource is removed from these YAML files,
Argo CD can remove it from the cluster during the next sync.

## Deploy with Argo CD

Argo CD must already be installed in your cluster. Apply the Application file:

```bash
kubectl apply -f application.yaml
```

Check the Argo CD Application:

```bash
kubectl get applications -n argocd
```

Check the deployed app resources:

```bash
kubectl get pods -n s12kunballi
kubectl get service -n s12kunballi
```

## Save and push your work

```bash
git add .
git commit -m "Add Argo CD application manifests"
git push
```
