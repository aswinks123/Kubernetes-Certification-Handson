
## Create a PV with 1GB size

```go
kubectl apply -f persistent_volume.yaml
```

![alt text](./images/image.png)

## Create a PVC and verify whethr PVC is bound to PV

```go
kubectl apply -f volume_claim.yaml
```

![alt text](./images/image-1.png)

## Create a Pod with Volume allocated using the PVC

```go
kubectl apply -f nginx_pod.yaml
```

![alt text](./images/image-2.png)

## Add data to the PV and verify whether its reflected in pod

![alt text](./images/image-3.png)

