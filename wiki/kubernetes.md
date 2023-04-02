# Storage
## Persistant Volume
### nfs share
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: (name)
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 10Gi
  nfs:
    path: ("export path")
    server: ("hostname to server")
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem
```
# Kube-vip
## Install
```
export VIP=10.0.1.30
export INTERFACE=enp6s18
KVVERSION=$(curl -sL https://api.github.com/repos/kube-vip/kube-vip/releases | jq -r ".[0].name")

#alias kube-vip="ctr run --rm --net-host ghcr.io/kube-vip/kube-vip:$KVVERSION vip /kube-vip"
alias kube-vip="/usr/local/bin/ctr image pull ghcr.io/kube-vip/kube-vip:$KVVERSION; /usr/local/bin/ctr run --rm --net-host ghcr.io/kube-vip/kube-vip:$KVVERSION vip /kube-vip"
kubectl apply -f https://kube-vip.io/manifests/rbac.yaml
mkdir -p /var/lib/rancher/k3s/server/manifests/
curl https://kube-vip.io/manifests/rbac.yaml > /var/lib/rancher/k3s/server/manifests/kube-vip-rbac.yaml
kube-vip manifest daemonset --interface $INTERFACE --address $VIP --leaseRetry 4 --leaseDuration 30 --leaseRenewDuration 20 --inCluster --taint --controlplane --services --arp --leaderElection | tee /var/lib/rancher/k3s/server/manifests/kube-vip.yaml
```

## Other
### Fix reboots
Change environment variables:
```
  - name: vip_leaseduration
      value: "30"
  - name: vip_renewdeadline
      value: "20"
  - name: vip_retryperiod
      value: "4"
```
