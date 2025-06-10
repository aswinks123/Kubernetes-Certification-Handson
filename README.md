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