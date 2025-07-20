# What is a StatefulSet?

A StatefulSet is a Kubernetes resource used to manage stateful applications.

Unlike a Deployment (which creates stateless pods), StatefulSet is designed for applications where each pod:

1. needs a stable network identity (name)
2. needs persistent storage that stays even if pod is deleted
3. must be created or terminated in order

## When to Use StatefulSet?

You run databases like MySQL, MongoDB, Cassandra.

You need unique, stable identities for pods (pod-0, pod-1, etc.).

Each pod needs its own storage (PVCs).


| Feature      | Deployment       | StatefulSet        |
| ------------ | ---------------- | ------------------ |
| Pod identity | Random           | Stable             |
| Pod storage  | Shared/ephemeral | Dedicated & stable |
| Pod ordering | No               | Yes                |
| Scaling      | Any order        | Ordered            |


# Deploy a Stateful Application

### 1.Create a Headless Service

Create a file: headless-service.yaml

```go 
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  clusterIP: None    # Makes it headless service
  selector:
    app: nginx
  ports:
  - port: 80
```

### 2.Deploy a StatefulSet
```go
apiVersion: apps/v1
kind: StatefulSet  #type of resource
metadata:
  name: web-stateful-set    #name f the stateful set, pods will be named web-stateful-set-0, web-stateful-set-1...etc
spec:
  serviceName: "nginx-headerless-service"     #Name of the headerless service that we created. This allows pods to get DNS hostnames like:web-stateful-set-0.nginx
  replicas: 3  #You want 3 pods running: wweb-stateful-seeb-0, web-stateful-se-1, web-stateful-se-2.
  selector:    #Selects pods with label app=nginx
    matchLabels:
      app: nginx
  template:   #Defines the pod template for the StatefulSet.
    metadata:
      labels:  #Adds label app=nginx to pods.
        app: nginx
    spec:    #Defines containers in each pod.
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80  #Exposes port 80 inside the pod (Nginx listens here).
        volumeMounts:   #Mounts a volume named www at /usr/share/nginx/html. Each pod will have its own persistent volume mounted there.
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:  #Create a PersistentVolumeClaim (PVC) for each pod.
  - metadata:
      name: www
    spec:
      accessModes: ["ReadWriteOnce"]  #Access mode is ReadWriteOnce : means only one pod can write to the volume at a time.
      resources:
        requests:
          storage: 1Gi  #Each PVC will request 1Gi of storage.
```

## Visual Representation

```go
Headless Service: nginx (clusterIP: None)
        |
  ----------------------------
  |           |            |
web-0       web-1        web-2
PVC:        PVC:         PVC:
www-web-0   www-web-1    www-web-2
```

## Verify the DNS endpoint

![alt text](image.png)