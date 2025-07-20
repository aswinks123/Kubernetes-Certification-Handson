## Deploy Headless Service

```go
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
  - port: 3306
```

## Deploy StatefulSet (3 replicas)

```go
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: mypassword
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

## Check Pods and PVCs

```go
kubectl get pods
kubectl get pvc
```
You will see

```go
Pods

mysql-0   Running
mysql-1   Running
mysql-2   Running

and PVCs

mysql-data-mysql-0
mysql-data-mysql-1
mysql-data-mysql-2

```

## Initialize Primary (mysql-0)