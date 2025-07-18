# Important Learning about configMap

### What is a configMap

A ConfigMap is a Kubernetes object that lets you store non-confidential configuration data as key-value pairs.

## Ways to create configMap

### Create from literal

```go
kubectl create configmap  first-configmap --from-literal=key1=value1 --from-literal=key2=value2
```

Better format to read 

```go
kubectl get cm first-configmap -o yaml
```

### From a file

```go
Create a file and add values in the format

key1=value1
key2=value2
then apply the config map

kubectl create cm second-configmap --from-file=large.txt
```

### From multiple files

when you have multiple files with config values, you can just point that directory

```go
kubectl create cm third-configmap --from-file=<path to that directory>  #All files will be added to config map
```

Note: There are multiple types of value supported 

eg: multiline data is also supported

## Note: Types of configMaps are explained inside the configMaps directory (please review it)

### Immutable configMap

1. By default, ConfigMaps are mutable. Once ConfigMaps is created as immutable, you cannot change or update them.

2. Attempting to modify an immutable ConfigMap will cause an error.

3. Ideal for production workloads where config stability is critical.

4. Ensures configs don’t change unexpectedly.

### How to create an immutable ConfigMap?

```go
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-immutable-config
immutable: true   #It enables immutable feature
data:
  key1: value1
  key2: value2
```


