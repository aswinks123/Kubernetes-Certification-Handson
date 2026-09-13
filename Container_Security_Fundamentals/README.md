# Container and Kubernetes Security Best Practise.

## Docker file Side:

1. Use small base image such as apline

2. No latest tag

3. No secrets

4. Multi stage build

5. No root user

Next step  - learn docker multi stage build using a pyhton example


## Vulnerabilities and Scanning

Concept of signed images - Learn it  

(Kubernetes adminssion controllers can verify the sign before allowing a pod to run)


## Container Registry

 - RBAC verifications (who can access, who can pull and push)

 - Immutable tags/Digest

 - Use private registery

 - Production only allow read only

 - Vulnerability scanning on registry



## Kubernetes Side:

- Set pod security context - non root user, readonlyrootFS, allow privilage escalation etc

Note: readOnlyRootFilesystem makes the container filesystem read-only, so only explicitly mounted writable locations can be modified

- Enforce RBAC. Follow least privilage principle

- Use namespaces and allow RBAC on namespaces. so one resource cannot access another. 

- Divide resources based on namespacs such as Dev, Stage etc

- Use network policies to deny traffic to a different pods. By default all pods can communicate with each other. This could be a serious threat where attaker can move from one pod to another compormising database credentials, API keys etc.

- Make sure your CNI supports network policies.

- Serice accounts must be carefully provided. it a customer service account is provided when creating that pod, we careful about what permissin that hold. Default is fine but careful when providing customer one with wider permissions.

- Make sure secrets are encrypted at etcd level at rest  (secreats are just base64 encoded and not encrypted, attacker can easily decode then using base64 --decode command)

- Although we encrypt etcd, the previously existing data will still exist unecrypted, so you can recreate the sectet or modify to make them encrypted.

- Use secret stores such as vault, AWS secrets manager.

- Rotate secrets.

- Integrate with SSO for authentication instead of putting a client certificate/key directly in the kubeconfig.

- Monitor run time attaks like bash spawn, openports, outbound traffic, DNS spoofing, hostfile forging etc.

- remove unnecessary linux capabilities of the container.

- Add kubernetes Admission controller to makes sure only allowed resource can be created, it cerifiesor mutates the incoming API request and decide whether to allow or deny.

- There are mainly 2 type of traffic in kuberntes. Make sre they are monitored and secured.

1. North-South : The traffic coming from the user and from the resource to outdside -> LB -> API Server->Pod etc... and vice versa

2. East-West : Pod to pod or service to service etc. Use MTLS to encrypt this traffice to prevent Man in middle attaks

Example usecase:

1. Use small base image such as apline

2. No latest tag

3. No secrets

4. Multi stage build

5. No root user


## Observability:

- Enable logging.

- Secutiry logs, Application logs, Audit logs etc.

- Monitor resource usages.

- Log retention.

- Have incident response plan.