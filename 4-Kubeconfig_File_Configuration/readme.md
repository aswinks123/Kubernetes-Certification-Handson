## What is a kubeconfig file?

It’s a YAML file used by kubectl and other Kubernetes clients.

It contains information about clusters, users, and contexts so you can connect and manage multiple Kubernetes clusters.

By default, kubectl looks for it at


```go
~/.kube/config
```

You can also specify another file with:


```go
kubectl --kubeconfig=/path/to/file
```

It’s essentially a map of:

Clusters (where to connect – API server)

Users (how to authenticate)

Contexts (a shortcut saying “use this cluster with this user”)

## A sample kubeconfig file

```go
apiVersion: v1  # Always v1 for kubeconfig.
kind: Config
preferences: {} # Usually empty {}
clusters:
#Details of cluster to connect and their connection methods
- name: dev-cluster  # First cluster
  cluster:
    server: https://dev.example.com:6443  # IP of cluster
    certificate-authority: /home/aswin/.kube/dev-ca.crt # Path to the CA file that validates API server cert.
- name: prod-cluster
  cluster:
    server: https://prod.example.com:6443
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0t...

users:  # Users who can log in
- name: dev-user
  user:  # Authentication method. Here certificate and key is used. It can also be token, username and password etc
    client-certificate: /home/aswin/.kube/dev-user.crt 
    client-key: /home/aswin/.kube/dev-user.key
- name: prod-user
  user:
    token: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...  # Token used for authetication

contexts:  # Context combines a cluster, user, and optional namespace together
- name: dev-context  # Context nameed dev-context
  context:
    cluster: dev-cluster
    user: dev-user
    namespace: dev-namespace
- name: prod-context # Context nameed prod-context
  context:
    cluster: prod-cluster 
    user: prod-user
    namespace: prod-namespace

current-context: dev-context  #This is the contect currently kubectl is working with
```

## To set current-context

kubectl config use-context prod-context

## Commands to manage kubeconfig

| Command                                   | Description                            |
| ----------------------------------------- | -------------------------------------- |
| `kubectl config view`                     | See current kubeconfig                 |
| `kubectl config get-contexts`             | List all contexts                      |
| `kubectl config current-context`          | Show current context                   |
| `kubectl config use-context <name>`       | Switch to a different context          |
| `kubectl config set-context ...`          | Create or modify a context             |
| `kubectl config unset ...`                | Remove users/clusters/contexts         |
| `KUBECONFIG=file1:file2 kubectl get pods` | Merge multiple kubeconfigs temporarily |
