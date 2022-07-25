# K3S post install
## Namespace
```
kubectl create namespace prod
kubectl create namespace dev
kubectl create namespace staging
```
## Repos
```
helm repo add traefik https://helm.traefik.io/traefik
helm repo add metallb https://metallb.github.io/metallb
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo add k8s-at-home https://k8s-at-home.com/charts/
helm repo add emberstack https://emberstack.github.io/helm-charts
helm repo add jetstack https://charts.jetstack.io
```

## Reflector
```
helm upgrade --install reflector emberstack/reflector -n kube-system
```

## Cert-manager
```
helm upgrade --install \
cert-manager jetstack/cert-manager \
--namespace cert-manager \
--create-namespace \
--set installCRDs=true
# --version v1.8.2 \
  
kubectl apply -f cloudflare-api-token-secret.yaml -n certmanager
kubectl apply -f letsencrypt-global-prod.yaml
kubectl apply -f egeback-com.yaml
kubectl apply -f egeback-se-tls.yaml
kubectl apply -f egeback-se-tls.yaml -n kube-system
kubectl apply -f egeback-se-tls.yaml -n prod
kubectl apply -f egeback-se-tls.yaml -n staging
kubectl apply -f egeback-se-tls.yaml -n dev
```

## Traefik
```
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
helm install traefik traefik/traefik -n kube-system -f values.yaml
```

## Rancher
```
kubectl create namespace cattle-system
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancher.internal.egeback.com \
  --set replicas=3 \
  --set ingress.tls.source=egeback-com-tls
```
