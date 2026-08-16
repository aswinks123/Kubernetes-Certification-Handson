# Kubernetes Imperative Commands for Deployment

1. Create a Deployment

kubectl create deployment my-deployment --image=nginx  

2. Create Deployment with replicas

kubectl create deploy my-deployment --image=nginx --replicas=2

replicas determine the desired umber of pods. Kubernetes controller ensure that the desired number is always maintained

3. Create Deployment in a namespace

kubectl create deploy my-deployment --image=nginx --replicas=2 -n test-ns

4. Scale Deployment

kubectl scale deployment my-deployment --replicas=3 or kubectl scale deployment my-deployment --replicas=2

5. Change Deployment image

kubectl set image deploymnt my-deployment nginx=nginx:1.27

nginx=nginx:1.27  means   <container name inside the deployment> = <image nameand tag of that container>

When changes happen and the pod needs to be recreated, Deployment performs a rolling update by gradually scaling the old ReplicaSet down and the new one up.


Other paramenters can also be chnages, example:


Environment variables: kubectl set env deployment my-deployment  APP_ENV=production, 

Resource and limits:

```
"kubectl set resources deployment web \
  --requests=cpu=100m,memory=128Mi \
  --limits=cpu=500m,memory=256Mi"
```

6. Rollout

A Kubernetes rollout is the process of gradually applying a change to a Deployment's Pod template and replacing the old Pods with new Pods.

when an image is changed in a deployemnt, it triggers a rollout that reduce the previous relica to 0 and new replica accordig to the replica decleration. How it replace depends on rollout strategies.

7. View Rollout history

kubectl rollout history deploy  my-deployment

```
aswin@Aswin-HP:tmp$ k rollout history deploy  my-deployment
deployment.apps/my-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

```

The CHANGE_CLAUSE is showing as none because we havent set it. If you want to add a reason or description of current deployment, you can add CHANGE-CAUSE

kubectl annoate deployment my-deployment kubernetes.io/change-cause="Updated image to latest"

```
aswin@Aswin-HP:tmp$ kubectl rollout history deploy  
deployment.apps/ my-deployment                                                     
REVISION  CHANGE-CAUSE
1         <none>
2         Updated the image to latest

```

kubernetes.io/change-cause="Updated image to latest" : This is actually a key value pair that is set in the deployment manifest under annotations:

kubectl rollout specifically look for this key "kubernetes.io/change-cause" and populte the value to the CHANGE-CAUSE field of the rollout history comamnd.