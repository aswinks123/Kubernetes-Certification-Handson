## Step 1: Deploy the service account, role, rolebinding

```go
kubectl apply -f serviceaccount.yaml
kubectl apply -f role-and-rolebinding.yaml
```
It creates a service account named: list-pod-sa in default namespace and also bind the pod-reader role with this service account.

## Step 2: Get the ServiceAccount token

In Kubernetes 1.24+, ServiceAccounts don’t automatically get Secrets. You need to create a token manually and bind it to the ServiceAccount.

kubectl create token list-pod-sa -n default #It will output a token

```go
eyJhbGciOiJSUzI1NiIsImtpZCI6IlEzR3h4MGJNVTNMdlZJTTRudzlLcTh6TkJ2eXJremIzdGc2bmJoc050SVEifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzUxODI4MDg3LCJpYXQiOjE3NTE4MjQ0ODcsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0Iiwic2VydmljZWFjY291bnQiOnsibmFtZSI6Imxpc3QtcG9kLXNhIiwidWlkIjoiNzA0MmUxNWMtY2ExYi00Njk1LTg3OTgtNDM2N2RjNGQ4NTY0In19LCJuYmYiOjE3NTE4MjQ0ODcsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0Omxpc3QtcG9kLXNhIn0.Jo0PdA6fWHFz-3JRp2uS-iD0mWEU2rUeIzs9b2rC1oL56YCvrTdZLPTVYWuwn7fBsGfKvEkcQG_o2ZR2uTH7PIXtw49zcNC0nUUxfc5d_946pcUanUueQRnKcEnJ92fdcGFxt_gMzm4E43niJ7ab3DEoB-xlolQXXxPv0ybs9uwff0thJckx_W0vQiF8SxRdH3WjHC3Bl7hNigxf-T5lD1EnnWpMvRQ5VkYiOFiKvQioisukqvYHR2R5in4aoJbhGs3SG0ObAKjthPNtZnSCu9xMTqVqyMtkjkryYJZXtVY-ERlE52rK8XLGa6FmCTGy5JDC5qq0eYJO-2D_VFKRfQ

```
## Step 3: Get the API server URL

kubectl cluster-info

```go
Kubernetes control plane is running at https://192.168.49.2:8443

```

## Step 4: Use curl with the token

```go
curl -k https://192.168.49.2:8443/api/v1/namespaces/default/pods --header "Authorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IlEzR3h4MGJNVTNMdlZJTTRudzlLcTh6TkJ2eXJremIzdGc2bmJoc050SVEifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNzUxODI4MDg3LCJpYXQiOjE3NTE4MjQ0ODcsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJkZWZhdWx0Iiwic2VydmljZWFjY291bnQiOnsibmFtZSI6Imxpc3QtcG9kLXNhIiwidWlkIjoiNzA0MmUxNWMtY2ExYi00Njk1LTg3OTgtNDM2N2RjNGQ4NTY0In19LCJuYmYiOjE3NTE4MjQ0ODcsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0Omxpc3QtcG9kLXNhIn0.Jo0PdA6fWHFz-3JRp2uS-iD0mWEU2rUeIzs9b2rC1oL56YCvrTdZLPTVYWuwn7fBsGfKvEkcQG_o2ZR2uTH7PIXtw49zcNC0nUUxfc5d_946pcUanUueQRnKcEnJ92fdcGFxt_gMzm4E43niJ7ab3DEoB-xlolQXXxPv0ybs9uwff0thJckx_W0vQiF8SxRdH3WjHC3Bl7hNigxf-T5lD1EnnWpMvRQ5VkYiOFiKvQioisukqvYHR2R5in4aoJbhGs3SG0ObAKjthPNtZnSCu9xMTqVqyMtkjkryYJZXtVY-ERlE52rK8XLGa6FmCTGy5JDC5qq0eYJO-2D_VFKRfQ"
```

## Output


![alt text](image.png)

Now the role is able to access the pod resource successfully


![alt text](image-1.png)

Verify the same for deployment and it failed due to permission denied as the role don't have permission to list deployments