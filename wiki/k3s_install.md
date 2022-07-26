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
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add longhorn https://charts.longhorn.io
helm repo add keel https://charts.keel.sh
helm repo add portainer https://portainer.github.io/k8s/

helm repo update
```

## Metallb
```
helm install metallb metallb/metallb --create-namespace -n kube-system
```
```
cat metallb.yaml

apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: lb-pool
  #namespace: metallb-system
  namespace: kube-system
spec:
  addresses:
  - 10.0.1.50-10.0.1.59 # IP range for LoadBalancer
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2
  #namespace: metallb-system
  namespace: kube-system
spec:
  ipAddressPools:
  - lb-pool
```

```
kubectl apply -f metallb.yaml
```

## Reflector
```
helm upgrade --install reflector emberstack/reflector -n kube-system
```

## Cert-manager
```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.9.0/cert-manager.crds.yaml
helm upgrade --install \
cert-manager jetstack/cert-manager \
--namespace kube-system \
--create-namespace \
--set installCRDs=true
# --version v1.8.2 \
  
kubectl apply -f cloudflare-api-token-secret.yaml -n kube-system
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
