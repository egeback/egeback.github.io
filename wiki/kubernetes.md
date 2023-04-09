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
