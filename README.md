# k8s.cedi.dev

This repository contains some of the initial cluster setup code for my K8s cluster on cedi.dev

## Early ToDos

[x] setup git-crypt
[x] Install [cert-manager](https://cert-manager.io/docs/installation/kubernetes/)
[ ] setup argocd to deploy the rest automated

## CertManager Setup:
```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
kubectl apply -f letsencrypt-staging.yaml
kubectl apply -f letsencrypt-prod.yaml
```

## ArgoCD Setup:

### Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Make ArgoCD API Server accessible
Patch ArgoCD to use `LoadBalancer` instead of `ClusterIP` in order to make ArgoCD API Server accessible
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

### Ingress for Traefik2

