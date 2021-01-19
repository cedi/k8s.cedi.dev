# k8s.cedi.dev

This repository contains some of the initial cluster setup code for my K8s cluster on cedi.dev

## Early ToDos

- [x] setup git-crypt
- [x] Install & Setup [cert-manager](https://cert-manager.io/docs/installation/kubernetes/)
- [ ] Install & Setup external-dns
- [x] Install ingress-nginx
- [ ] setup argocd to deploy the rest automated

## CertManager Setup:
```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
kubectl apply -f letsencrypt-staging.yaml
kubectl apply -f letsencrypt-prod.yaml
```

## ingress-nginx setup
Install ingress-nginx in the Scaleway flavour (as I'm running on Scaleway)
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/scw/deploy.yaml
```

Patch externalTrafficPolicy to Cluster
```
kubectl -n ingress-nginx patch service ingress-nginx-controller --patch "$(cat ingress-nginx-controller-cluster-policy.patch.yaml.yaml)"
```

## ArgoCD Setup:

Install ArgoCD with
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Configure ArgoCD to allow insecure connections (as we terminate TLS on the ingress)
Patch ArgoCD service to allow insecure connection, as we do tls offloading in ingress-nginx
```
kubectl -n argocd patch deployment argocd-server --patch "$(cat argocd-server-insecure.patch.yaml)"
```

SSL-Pastrough with cert-manager and Let's Encrypt
```
kubectl apply -f ingress-nginx-argocd.yaml
```

