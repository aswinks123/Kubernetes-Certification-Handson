# Kubernetes Imperative POD Commands and Yaml Representation in Data Structures

1. Create a Pod 

kubectl run nginx --image=nginx   # Creates a pod, not a deployment

2. Create a Pod with a container port

kubectl run nginx-with-port --image=nginx --port=80

--port is used to express that the service inside the container is listening on that port. It has zero impact on actual application port inside the container. It helps kubernetes services to use it as a target.

3. Create Pod with environment variable

kubectl run nginx-with-env --image=nginx --env="APP_TYPE"="PRODUCTION"

Inject directly into the container process memory when the container launches. Restart of Pod required for modification. To solve this volume mounts can be used.

4. Create Pod with command/args

kubectl run ngnx --image=nginx --command -- echo "Hello World!"

--command overrides the image's ENTRYPOINT

Docker ENTRYPOINT = Kubernetes Command

Docker CMD = Kubernetes args

--command replaces the default ENTRYPOINT of the docker image.

5. Create Pod in a namespace

 kubectl run pod --image=nginx -n test-ns

The namespace must already exist. kubectl run won't create it for you.

The namespace applies to the resource you're creating, not to the image or container.

6. Generate Pod YAML

kubectl run nginx --image=nginx --dry-run=client -o yaml

--dry-run=client -  generate locally without sending the object to the API server.

-o yaml - output the generated resource as YAML.

To save the output as a file =   kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml




# How a simple POD spec is represented in data structure?


## Sample pod spec:

```
spec:
  containers:
  - image: nginx
    name: my-container-1
    env:
        - name: APP
          value: PROD
    ports:
        - containerPort: 80
```

## Internal data structure representation:

```
spec (object)
└── containers (list)
    ├── container object
    │   ├── image
    │   ├── name
    │   ├── env (list)
    │   └── ports (list)
    │
    └── container object
        ├── image
        └── name
```

Every - under containers represents one container object in the containers list.



## Reference model

```
Kubernetes Resource (object)
│
├── apiVersion → string
├── kind       → string
│
├── metadata → object
│   ├── name      → string
│   ├── namespace → string
│   └── labels    → object
│       └── app → string
│
└── spec → object
    └── containers → list
        └── item → object
            ├── name  → string
            └── image → string

```