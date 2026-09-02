# Argo CD Application

This repository contains Kubernetes files for deploying `myapp` with Argo CD.

## What is in this repository?

```text
.
├── application.yaml
└── dev/
    ├── deployment.yaml
    └── service.yaml
```

`dev/deployment.yaml` runs two copies of the application. Each copy is a Pod
using the `nanajanashia/argocd-app:1.2` image on port `8080`.

`dev/service.yaml` gives the Pods a stable name, `myapp-service`, and sends
traffic on port `8080` to Pods with the label `app: myapp`.

`application.yaml` is the Argo CD instruction. It tells Argo CD to read the
`dev` folder in this repository and deploy it to the `myapp` namespace.

## Argo CD settings

Argo CD watches this branch:

```text
feature/branch-s12kunball-argocd-deploment
```

Automatic sync is configured with:

- `CreateNamespace=true`: create the `myapp` namespace if it does not exist.
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
kubectl get pods -n myapp
kubectl get service -n myapp
```

## Save and push your work

```bash
git add .
git commit -m "Add Argo CD application manifests"
git push
```
