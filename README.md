# Hands on learnings for Certified Kubernetes Administrator exam

```sh
“ You have the right to perform your prescribed duties, but you are not entitled to the fruits of your actions. Never consider yourself the cause of the results of your activities, nor be attached to inaction. ”

— Bhagavad Gita 2.47
```

## Important finding from my handson learnings.


#To list all the resources 

kubectl api-resources

#Validate the command using dry run

command --dry-run=client #validate the command. it will not create resources

#Generate a manifest output along with resource creation 

kubectl run nginx2 --image=nginx -o yaml

#Generate the manifest output only with out creating resource

kubectl run nginx2 --image=nginx --dry-run=client -o yaml

#To log in to a specific containet in multicontainer pod

kubectl exec -it multicontainer-pod -c <container-name> /bin/sh

#To see details and structure of a resource

kubectl explain pods


Note: You use expose in Docker file to show the person who runs the image, in which port the app listens(not to open the port). this port will be listed under the 'PORTS' in docker ps output

#To allow port forwarding

kubectl port-forward pod/nginx 8080:80

 #To Port-forward the service

 kubectl port-forward service/my-service 8080:80

#To Port-forward the ingress

 kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80


#To add label to node

kubectl label node minikube disktype=ssd

#To remove a label from a node

kubectl label node minikube disktype-

#To see the endpoints of a service

kubectl get endpoints <service name>

#View resource from all namesapces

kubectl get pods --all-namespaces  or kubectl get pods -A

#To use service account in a pod to call kubernetes cluster

curl -k -H "Authorization: Bearer $token" https://192.168.49.2:8443/api/v1  #token is located at /var/run/secrets/kubernetes.io/serviceaccount inside the pod

#Create a horizontal pod autoscaler

kubectl autoscale deployment <name of deploy> --cpu-percent=50 --min=1 --max=3

#The DNS name of a Service follows this format: <service-name>.<namespace>.svc.cluster.local

#To view the cluster infor such as IP and DNS

kubectl cluster-info

In kuberenets the default authorization mode is AlwaysAllow

#Create a token in kubernetes. that can be attached to a service account

kubectl create token <service account name> --duration=10h # duration is expiry of thge token

#To find the resources that are part of a particular api group

kubectl api-resources --api-group="apps"  or kubectl api-resources --api-group=""  fo coreapi resources like pods

#Log in to Minikube

minikube ssh

