# Kubevip
# Setup
```
# Configure what IP that should act as VIP and interface to bind to
export VIP=10.0.1.30
export INTERFACE=enp6s18

# Get latest kube version
KVVERSION=$(curl -sL https://api.github.com/repos/kube-vip/kube-vip/releases | jq -r ".[0].name")

kubectl apply -f https://kube-vip.io/manifests/rbac.yaml

mkdir -p /var/lib/rancher/k3s/server/manifests/
curl https://kube-vip.io/manifests/rbac.yaml > /var/lib/rancher/k3s/server/manifests/kube-vip-rbac.yaml

# Download kube vip
# Adds leaseRetry, leaseDuration, leaseRenewDuration adjustments
alias kube-vip="/usr/local/bin/ctr image pull ghcr.io/kube-vip/kube-vip:$KVVERSION; /usr/local/bin/ctr run --rm --net-host ghcr.io/kube-vip/kube-vip:$KVVERSION vip /kube-vip"
kube-vip manifest daemonset --interface $INTERFACE --address $VIP --leaseRetry 4 --leaseDuration 30 --leaseRenewDuration 20 --inCluster --taint --controlplane --services --arp --leaderElection | tee /var/lib/rancher/k3s/server/manifests/kube-vip.yaml
```
