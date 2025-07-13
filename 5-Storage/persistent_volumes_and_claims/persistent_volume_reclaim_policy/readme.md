## Persistent Volume reclaim policy

The reclaimPolicy defines what Kubernetes should do with a PersistentVolume (PV) when its bound PersistentVolumeClaim (PVC) is deleted.

Storage class can be in either of these reclaim policy.

| **Policy** | **What Happens When PVC is Deleted?**                   | **PV Status** | **Storage Backend**                 |
| ---------- | ------------------------------------------------------- | ------------- | ----------------------------------- |
| `Retain`   | PV stays, storage stays. Manual cleanup needed.         | `Released`    | Storage is **NOT deleted**          |
| `Delete`   | PV is deleted and so is the storage in cloud/NFS/etc.   | Gone          | Storage is **deleted**              |
| `Recycle`  | Wipes storage (`rm -rf /data/*`) and makes PV reusable. | `Available`   | Storage wiped clean (⚠️ deprecated) |

## Where do you set it?

On a StorageClass:

```go
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs
reclaimPolicy: Delete  #Delete reclaim policy

```

## On a PersistentVolume:

```go
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain  #Retain reclaim policy

```





