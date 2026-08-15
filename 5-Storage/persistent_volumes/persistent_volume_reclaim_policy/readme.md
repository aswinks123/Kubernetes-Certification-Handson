## Persistent Volume reclaim policy

The reclaimPolicy defines what Kubernetes should do with a PersistentVolume (PV) when its bound PersistentVolumeClaim (PVC) is deleted.

Storage class can be in either of these reclaim policy.

| **Policy** | **What Happens When PVC is Deleted?**                   | **PV Status** | **Storage Backend**                 |
| ---------- | ------------------------------------------------------- | ------------- | ----------------------------------- |
| `Retain`   | PV stays, storage stays. Manual cleanup needed.         | `Released`    | Storage is **NOT deleted**          |
| `Delete`   | PV is deleted and so is the storage in cloud/NFS/etc.   | Gone          | Storage is **deleted**              |
| `Recycle`  | Wipes storage (`rm -rf /data/*`) and makes PV reusable. | `Available`   | Storage wiped clean (deprecated) |

## Where do you set it?

1. On a StorageClass:

```go
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs
reclaimPolicy: Delete  #Delete reclaim policy

```

2. On a PersistentVolume:

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

## Can we use the PV after its reclaimed when using retain policy?

When a PVC or PV is deleted the underlyign store is still preserved, but can't be used by any other pods.

## How to revover the PV after the PV is deleted whe nusing reclaim policy

If someone accidently delete the PVC, It can be recovered by editing the PV and removing the "claimRef:" section.

```go
kubectl edit pv mypv
```

Search for the following section and delete it

```go
claimRef:
  name: mypvc
  namespace: default
  uid: <old-pvc-uid>
```

Now the PV becomes Available again. 

Recreate the PV with same specs : Recreate the deleted PVC with the exact same name, size, storageClass, accessModes, and namespace as the original

Then bind it to the new PVC

