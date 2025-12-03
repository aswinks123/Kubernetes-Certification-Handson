
# What will happen when an invalid argument is added to kube api-server's  manifest file.



Lets add an invalid argument to : /etc/kubernetes/manifests/kube-apiserver.yaml  (api-server manifest file)


Configure the api-server manifest with a new argument --this-is-very-wrong.


Before doing :

Always make a backup !

```
cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver.yaml.ori
```

Make the change


```
vim /etc/kubernetes/manifests/kube-apiserver.yaml


apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 172.30.1.2:6443
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=172.30.1.2
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-issuer=https://kubernetes.default.svc.cluster.local
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=10.96.0.0/12
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key

# This is the extra argument we added to produce an api server crash

    - --this-is-very-wrong


    image: registry.k8s.io/kube-apiserver:v1.34.1
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 172.30.1.2

```


# Wait till container restarts


watch crictl ps


Notice the api-server pod is removed automatically!

```
controlplane:~$ crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD                                       NAMESPACE
a0625b5161558       c80c8dbafe7dd       34 seconds ago      Running             kube-controller-manager   3                   042c1e2dfbf4c       kube-controller-manager-controlplane      kube-system
f3da8a6ac5b00       7dd6aaa1717ab       34 seconds ago      Running             kube-scheduler            3                   56ff080c54c9f       kube-scheduler-controlplane               kube-system
5fa632b6fcb3b       f9c3c1813269c       5 minutes ago       Running             calico-kube-controllers   8                   09d41098de52a       calico-kube-controllers-7bb4b4d4d-5x7wv   kube-system
8686b7fc00270       dd1b26a6fc180       About an hour ago   Running             local-path-provisioner    1                   6a63efaaddf60       local-path-provisioner-76f88ddd78-srw9b   local-path-storage
f940ab41bf154       52546a367cc9e       About an hour ago   Running             coredns                   1                   57781bcf2bb3c       coredns-76bb9b6fb5-l64f8                  kube-system
53254612f7fd5       52546a367cc9e       About an hour ago   Running             coredns                   1                   fb3be7ff81365       coredns-76bb9b6fb5-kxd69                  kube-system
8d45fb25f3ce0       e6ea68648f0cd       About an hour ago   Running             kube-flannel              1                   121e260a624b5       canal-x8gdv                               kube-system
b9be57462fb25       75392e3500e36       About an hour ago   Running             calico-node               1                   121e260a624b5       canal-x8gdv                               kube-system
fc9d4a9633957       fc25172553d79       About an hour ago   Running             kube-proxy                1                   ed3fc08783fbe       kube-proxy-k5v4v                          kube-system
ae8aa73f55b65       5f1f5298c888d       About an hour ago   Running             etcd                      1                   8459f4868eb91       etcd-controlplane                         kube-system
```


# Check for apiserver pod staus


Note: as api-server is down, all command execution will be blocked as api-server is the entry point to kubernetes.

```
controlplane:~$ kubectl get pet pods -n kube-system

E1203 04:57:09.393864   35913 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://172.30.1.2:6443/api?timeout=32s\": dial tcp 172.30.1.2:6443: connect: connection refused"
E1203 04:57:09.394251   35913 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://172.30.1.2:6443/api?timeout=32s\": dial tcp 172.30.1.2:6443: connect: connection refused"
E1203 04:57:09.395881   35913 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://172.30.1.2:6443/api?timeout=32s\": dial tcp 172.30.1.2:6443: connect: connection refused"
The connection to the server 172.30.1.2:6443 was refused - did you specify the right host or port?
```


# Check the api-server log file

#Kubernetes stores the log file under: /var/log/pods/

```
root@minikube:/home/docker# cd /var/log/pods/

root@minikube:/var/log/pods# ll
total 36
drwxr-x--- 9 root root 4096 Dec  3 05:00 ./
drwxr-xr-x 4 root root 4096 Nov 26 05:35 ../
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_coredns-66bc5c9577-df77q_86c56505-44cd-4a41-8210-85c60652315f/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_etcd-minikube_e3a36fac0ae701bc11fad0a6716eec2c/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_kube-apiserver-minikube_8312b4cdc4b705c0e12f63794469cfad/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_kube-controller-manager-minikube_3b51c8241e224d47681cce32ea99b407/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_kube-proxy-xx4qg_5e9eb65d-5155-4a4c-8330-468f5dcc672e/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_kube-scheduler-minikube_dc6cf0a7bcb54d1f95cecc4d7b6b7d67/
drwxr-xr-x 3 root root 4096 Nov 26 05:35 kube-system_storage-provisioner_bb2286d8-560f-4957-9651-fbb160e972a3/
```

# Open the log file and check the content

```
controlplane:/var/log/pods/kube-system_kube-apiserver-controlplane_9a31fbe7182532487142fb3b5b2d2a75/

controlplane:/var/log/pods/kube-system_kube-apiserver-controlplane_9a31fbe7182532487142fb3b5b2d2a75/
kube-apiserver$ ll

total 12
drwxr-xr-x 2 root root 4096 Dec  3 04:57 ./
drwxr-xr-x 3 root root 4096 Dec  3 04:56 ../
-rw-r----- 1 root root   82 Dec  3 04:57 4.log


#cat the file

kube-apiserver$ cat 4.log 

#You can see the error listed!

2025-12-03T04:57:47.799512909Z stderr F Error: unknown flag: --this-is-very-wrong

```



# Now undo the change and rescue the api-server


Restore the backup 

```
cp ~/kube-apiserver.yaml.original /etc/kubernetes/manifests/kube-apiserver.yaml
```

Wait till container restarts

```
watch crictl ps



Every 2.0s: crictl ps                                                                                                                                controlplane: Wed Dec  3 05:09:29 2025

CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD
 NAMESPACE
43cff8ea51504       c3994bc696102       19 seconds ago      Running             kube-apiserver            0                   15032853fff8b       kube-apiserver-controlplane
 kube-system
40e11c1fb27e3       c80c8dbafe7dd       13 minutes ago      Running             kube-controller-manager   2                   042c1e2dfbf4c       kube-controller-manager-controlplane
 kube-system
17c51e9556d51       7dd6aaa1717ab       13 minutes ago      Running             kube-scheduler            2                   56ff080c54c9f       kube-scheduler-controlplane
```


# Check the apiserver pod

```
kubectl -n kube-system get pod

controlplane:~$ kubectl -n kube-system get pod
NAME                                      READY   STATUS             RESTARTS        AGE
calico-kube-controllers-7bb4b4d4d-5x7wv   0/1     CrashLoopBackOff   7 (3m39s ago)   15d
canal-x8gdv                               2/2     Running            2 (78m ago)     15d
coredns-76bb9b6fb5-kxd69                  1/1     Running            1 (78m ago)     15d
coredns-76bb9b6fb5-l64f8                  1/1     Running            1 (78m ago)     15d
etcd-controlplane                         1/1     Running            1 (78m ago)     15d

#====================== api server is back online ===========

kube-apiserver-controlplane               1/1     Running            0               15d


kube-controller-manager-controlplane      1/1     Running            2 (14m ago)     15d
kube-proxy-k5v4v                          1/1     Running            1 (78m ago)     15d
kube-scheduler-controlplane               0/1     Running            2 (13m ago)     15d

```




# We have successfully restored the kube api-server.
