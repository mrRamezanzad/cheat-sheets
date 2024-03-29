# more detailed commands of this course in https://gitlab.com/nanuchi/k8s-in-1-hour 
Kubernetes is very pupular and complex technology
i an open source container orchestration tool developed by google helps manage conteainerazed applications
because of microservices and increased usage of containers we need this container orchestration tool

- hish availability or no downtime
- scalability or high performance (scale up and down fast)
- disaster recovery - backup and restore

kubernetes has control plane node (primary "node agent" or master node) and bunch of kubeletes (slave nodes) 
- on master node (control plane nodes [can be multiple for backup at least 2] ) serveral K8s important processes are running to manage clusters, have handful of master processes an are much more important
 - API Server: which is entery point to K8s cluster (for UI, API, CLI)
 - control manager: keeps track of whats happening in the cluster
 - scheduler: ensures Pods (container) placements on different nodes based on node loads and needs
 - etcd: key value storage which has all the configuration data of each node and pod in it and using etcd snapshot we can backup and restore everything
 - virtual network: creates  one unified machine and helps nodes talk to each other
- applications will run on slave nodes so have hishter workload and much bigger and more resources

kubernetes components:
- Node: virtual or physical machine
- Pod: smallest unit in k8s, an abstraction over a container (usually 1 application per pod, ex: DB pod).
each Pod has it'ts internal IP address to talk with each other in virtual network which are ephemeral(can be killed due to lack of resources) and get new IP over each re-creation to pemanent Ip address we use Services  which is not depend on Pods lifecycle
* pods communicate with each other using services
- Service: two type of serveses are: - External Service (Http server of node.js and client) and Internal Service (db and private services) which is default type
- Ingress: istead of service, request goes to the Ingress and will be forwarded into services to protect protocol and ports
- ConfigMap: external configuration of application (global env)for non-confidential data only 
- Secret: used to store secret data, by default secrets are stored unencrypted so we should do one of the below:
  - enable encryptiuon at Rest for secrets.
  - enable RBAC (role based access control) rules to restrict reading access
- Volume: storage on local machine or remote outside of K8s cluster
- Deployment (for stateLESS APPS): Blueprint for "my-app" Pods which handles creating replication of same application for example in two or three nodes (load balancer) , scale up and down happens here
* DB's can't be replicated by deployment because they have state (their data)
- StatefulSet(for stateFUL APPS): two manage database replication, makes sure db's are syncronized, it has challanges and db are often hosted outside K8s cluster
- DaemonSet

Kubernetes Configuration
all configuation goes into API SERVER / Control Plane (master node)
kubectl - K8s CLI
UI, API and CLI requests to API SERVER must be either in YAML or JSON format for Deployment example below (a template for creating posds) :
```yaml
apiVersion: apps/v1 // declaring what we want to create
kind: Deployment  // declaring what we want to create it can also be a 'Service' for example
metadata:
  name: my-app
  labels: 
    app: my-app
spec:
  replicas: 2
  selector: 
    matchLabels:
      app: my-app
  template: 
    metadata: 
      labels: 
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-image
          env: 
            - name: SOME_ENV
              value: $SOME_ENV
          ports:
            - containerPort: 8080
```

every configuration file has 3 parts
1- metadata of component: name of component
2- spec (specification): configuration we want to apply which attributes differ based of kind of component we are defining metadata example below:

// nginx-deployment.yaml
```yaml
apiVersion: apps/v1 
kind: Deployment
metadata:
  name: nginx-deployment
  labels: ...
spec:
  replicas: 2
  selector: ...
  template: ...
```

// nginx-service.yaml
```yaml
apiVersion: v1 
kind: Service
metadata:
  name: nginx-service
spec:
  selector: ...
  ports: ...
```

3- status: automatically generated by K8s, etcd holds the current status of any K8s component

# what is minikube?
one node cluster which has master and worker process both on one maching for our test/local porpuse (we dont need to create big clusters anymore) with pre-installed docker
* we use Minikube CLI for start up / deleting the cluster

# what is kubectl?
it is a command line toll for K8s cluster that helps interact with minikube 
Api server enables interaction with cluster with UI, API or CLI (KUBECTL which is most powerful of 3 clients)zzzx
*z we use Kubectl CLI for configuring minikube cluster 

$ minikube status // shows status of our minikube
$ kubectl get node // shows all available nodes to interact with in our case minikube

# demo app 
we create 8 K8s config files
  1- ConfigMap: MongoDB Endpoint
  2- Secret: MongoDB User & Pwd
  3- Deployment & service: MongoDB application with internal service
  4- Deployment & service: Our own WebApp with external service
  
  # mongo-config.yaml
  ``` yaml
  apiVersion: v1
  kind: ConfigMap
  metadata: 
    name: mongo-config
  data: 
    mongo-url: mongo-service 
```
  
  # mongo-secret.yaml
  ``` yaml
  apiVersion: v1
  kind: Secret
  metadata: 
    name: mongo-secret
  type: Opaque // default for arbitrary key-value pairs
  data: 
    mongo-user: Alex   //to encode these secret values in ubuntu we can use this commmand  `$ echo -n password-to-encode | base64`
    mongo-password: ;ALDKFJSALKFKSJFLKSFSLKADF
```
  
  # mongo.yaml // Deployment * Service in 1 file, because they belong together. all deployments need services
  ``` yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata: 
    name: mongo-deployment
    labels: // for components label is optional but it's good practice to set them
      app: mongo 
  spec:
    replicas: 3
    selector: // shows which Pods belong to Deployment
      matchLabels:
        app: mongo
      template: //configuration for pod because Deployment manages Pod
        metadata:
          labels: // for pods label is required to identify similar posd in replicas 
            app: mongo // labels key values can be anything (instead of `app: mongo` we can set 'mykey: myvalue' but standard is to set 'app' key for application
        spec: 
          containers:
          - name: mongodb
            image: mongo:5.0
            ports:
            - containerPort: 27017
            env:
            - name: MONGO_INITDB_ROOT_USERNAME
              value: myuser  // directly putting value into env variable instead of using secret config
            - name: MONGO_INITDB_ROOT_PASSWORD
              value: 
              valueFrom: // setting value from secret config file
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-password
              
            
---  // these dashes is a yaml syntax to have multiple configurations within 1 file
  apiVersion: v1
  kind: Service
  metadata: 
    name: mongo-service
  spec:
    selector: // select pods to forward the requests to
      app: mongo
    ports: 
      - protocol: TCP
        port: 80  // service port that client can see and send request
        targetPort: 27017  // must be same as containerPort of Deployment
```
  
  # webapp.yaml
  ``` yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata: 
    name: webapp-deployment
    labels:
      app: webapp 
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: webapp
      template:
        metadata:
          labels:
            app: webapp
        spec: 
          containers:
          - name: webappf
            image: nanajanashia/k8s-demo-app:v1.9
            ports:
            - containerPort: 3000
            env: 
            - name: USER_NAME
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-user
            - name: USER_pwd
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-password
            - name: DB_URL
              valueFrom:
                configMapKeyRef: 
                  name: mongo-config
                  key: mongo-url
              valueFrom:
                secretKeyRef:
                  name: mongo-secret
                  key: mongo-user      
            
---
  apiVersion: v1
  kind: Service
  metadata: 
    name: webapp-service
  spec:
    type: NodePort # default is'ClusterIP' for internal services and NodePort is for external services
    selector:
      app: webapp
    ports: 
      - protocol: TCP
        port: 3000
        targetPort: 3000
        nodePort: 30100// exposes the service on each node's ip at a static port <NodeIP>:<NodePort> range must be between 30000-32767
```

$ kubectl get pod // to get running pods / abstract containers
$ kubectl apply -f mongo-config.yaml // apply manages applications through files defining K8s resources
$ kubectl apply -f mongo-secret.yaml
$ kubectl apply -f mongo.yaml
$ kubectl apply -f webapp.yaml

$ kubectl get all // gets all components that are deployed in the cluster
$ kubectl get configmap
$ kubectl get secret
$ kubectl describe service webapp-service // get detailed information of any component
$ kubectl logs webapp-deployment-dcffd6bcc-gsmlt -f // -f to continue streaming the logs  // get pod name by 'kubectl get pod'
$ kubectl get svc // getting services
$ kubectl get node // gets nodes which here is minikube
$ kubectl get node - o wide // wide option gives us more informations containing internal-ip address

