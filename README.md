# Hands on learnings for Certified Kubernetes Administrator exam


## Key learnings

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

#To add label to node

kubectl label node minikube size=Large