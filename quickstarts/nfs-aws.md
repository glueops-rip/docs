
## Configuring an NFS Mount

Requirements:
Access to your application Stack repository<br>

1. Go to your application stack repository.
2. Copy this template file below and save it as: `nfs.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-primary-nfs
spec:
  storageClassName: primary-nfs
  capacity:
    storage: 1000Gi # Adjust based on your needs
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  mountOptions:
      - timeo=600
      - retrans=2
      - nfsvers=4.1
      - rsize=1048576
      - wsize=1048576
      - noresvport
      - hard
  nfs:
    path: /
    server: REPLACE_THIS_VALUE
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-primary-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: primary-nfs
  resources:
    requests:
      storage: 1Gi
```

The one change you will need to make is updating: `REPLACE_THIS_VALUE` to use the `hostname` OR `IP` of your NFS Server. Also, the preset `mountOptions` above assume we are using AWS EFS.
