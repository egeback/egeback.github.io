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
## Schedule deployment on control-plane node
```
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      affinity:
        nodeAffinity:
            preferredDuringSchedulingIgnoredDuringExecution:
              - preference:
                  matchExpressions:
                    - key: node-role.kubernetes.io/master
                      operator: In
                      values:
                        - 'true'
                weight: 100
              - preference:
                  matchExpressions:
                    - key: node-role.kubernetes.io/control-plane
                      operator: In
                      values:
                        - 'true'
                weight: 100
              - preference:
                  matchExpressions:
                    - key: node-role.kubernetes.io/control-plane
                      operator: In
                      values:
                        - 'true'
                weight: 100
```
## Force scheduling on differnt nodes
Example with pods with label app.kubernetes.io/name = adguard-home
```
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
              - podAffinityTerm:
                  labelSelector:
                    matchExpressions:
                      - key: app.kubernetes.io/name
                        operator: In
                        values:
                          - adguard-home
                  topologyKey: kubernetes.io/hostname
                weight: 100
```
