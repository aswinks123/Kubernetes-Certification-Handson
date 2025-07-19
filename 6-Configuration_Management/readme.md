## Important Learning about config map

### Create from literal


kubectl create configmap  first-configmap --from-literal=key1=value1 --from-literal=key2=value2

Better format to read 

kubectl get cm first-configmap -o yaml


From a file
---
#Create a file and add values in the format
key1=value1
key2=value2
the apply the config map

kubectl create cm second-configmap --from-file=large.txt

From multiple file
----
when you have multiple files with config values, you can just point that directory
kubectl create cm third-configmap --from-file=<path to that directory>  #All files will be added to config map

Note: There are multiple types of value supported 

eg: multiline data is also supported