# Kubernates

Kubernates is a powerfull software used to manage containers in production. With the kubernates is possible create
escalable applications.

![img.png](img.png)

## [Basic kubernates components](https://kubernetes.io/docs/concepts/architecture/)

- **Cluster:** is a set of nodes;
- **Node:** A node is a worker machine where the kubernate is run;
- **Master:** is a node that controls the cluster;
- **Pod:** is a conteiner that runs in a node. A pod can have one or more containers;

## [Control Plane components](https://kubernetes.io/docs/concepts/overview/components/#kubelet)

- **Kube-apiserver:** this component is API used for users (panel admins, CLI's etc.) and other components for control
  the kubernates;
- **etcd:** is a key-value store used for store neccessary configurations for manage nodes, pods and clusters;
- **kube-scheduler:** is a component that schedule pods to run in the nodes;
- **kube-controller-manager:** is a component that runs controllers of the kube. Is the brain of the cluster;
- **container-runtime:** is the responsible for run the containers (usually docker);
- **kubelet:** is a component that runs in each node and is responsible for manage the node and the pods in the node;

## Kubernates workloads

In kubernates is possible create workloads using yaml or json files and CLI commands. In the next captions we
see as create pods, replicasets, deployments and services using yaml files.

### [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)

In kubernates is possible to define a pod in a node using commands and yaml files. The yaml files is most recomended
because with the files is possible to versioning the definitions. Bellow has a example of a pod definition:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx
      env:
        - name: EXAMPLE_ENV
          value: env-value
```

The properties apiVersion, kind, metadata and spec e default os all definitions in kubernates. In the **apiVersion** the
value is **v1** for pods, the **kind** is Pod, the **metadata** is the **name** of the pod and the **spec** are the
definition of the pod. In the spec we describind the containers that will run in the pod and your properties, as
enviroments variables and the image.

For create a pod using a describe yaml file is necessary use the command **kubectl apply**:

```shell
kubectl apply -f pod-definition.yaml
```

#### [Pod commands](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

Is possible create a pod using the command **kubectl run**. Bellow has a example of a pod creation using the command:

```shell
kubectl run pod-example --image=nginx
```

For manage created pods we're using bellow commands:

```shell
kubectl get pods # list all pods
kubectl get all # list all resources created
kubectl describe pod <pod-name> # return all informations about created pods
kubectl delete pod <pod-name> # delete the pod
kubectl edit pod <pod-name> # open a atual description of the pod for editing
kubectl get pods -o wide # return informations about pods with more details
```

### [Replicasets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

Replicasets is a workload resource for manage the pods. The replicasets is used to create and manage pods. Whith
replicasets is
possible create many replicas of the pods. Bellow has an example of a replicasets definition:

```yaml
apiVersion: devs/v1
kind: ReplicaSet
metadata:
  name: myapp-replica-set
  labels:
    app: myapp
spec:
  selector:
    matchLabels:
      type: front-end
  replicas: 3
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
          env:
            - name: EXAMPLE_ENV
              value: env-value
```

In these definitions whe have somes equals properties of the pod definition, but in the **kind** and **apiVersion** we
have diferents values. For the apiVersion the values is **apps/v1** and the kind we have **ReplicaSet**. In the spec
we're describing the **replicas quantity** in the propertie **replica** and in the **template** propertie the definition
for of the pods. The template is equals the pod definition.

For apply the replicasets definition is necessary use the command **kubectl apply**:

```shell
kubectl apply -f replicasets-definition.yaml
```

#### [Reoplicaset commands](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

The kubectl no has commands for create replicasets, but is possible manage the replicasets using the bellow commands:

```shell
kubectl get replicasets # list all replicasets
kubectl describe replicasets # return all informations about created replicasets
kubectl describe replicasets <replicaset-name> # return all informations about replicaset
kubectl get replicasets -o wide # return informations about replicasets with more details
kubectl delete replicaset <replicaset-name> # delete the replicaset
```

### [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

Deployment is a workload resource used to manage pods and replicasets. With the deployment is possible create, destroy,
and run
rollback in fail case. The deployments is very interestin because is possible create state for the aplication, and its
automatizate the deployment os pods os the replicaset. For example if you need change a new version of the images of the
pods, with the deployment this action is automatizated: the deployment create a new replicaset with the new version, and
replace the pods os the old replicaset for the pods of new replicaset. Is powerfull!

Deployment has two strategies for create the new replicaset: **Recreate** and **RollingUpdate**. In the **Recreate**
strategy all pods are killed and then the new pods was started. In the **RollingUpdate** strategy the new replicaset is
created and each old pod is stoped and replaced by new started pod of new replicaset.

Bellow has a example of a deployment definition:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: mywebsite
    tier: frontend
spec:
  replicas: 4
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
      containers:
        - name: nginx
          image: nginx
  selector:
    matchLabels:
      app: myapp
```

In these defintion the kind is Deployment, the metadata is simililar the others resources, but in the spec we have the
properties of
definition a replicaset.

For apply the deployment definition is necessary use the command **kubectl apply**:

```shell
kubectl apply -f deployment-definition.yaml
```

#### [Deployment commands](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

For create a deployment is necessary use the command **kubectl create**. When we create a deployment using the command,
the kubectl automatically create a replicaset and a pod. Is possible informate the replicas quantity with
the `--replicas`
flag. Bellow has a example of a deployment creation:

```shell
kubectl create deployment deployment-example --image=nginx --replicas=3
```

For manage the deployments we're using bellow commands:

```shell
kubectl set image deployment/<deployment-name> www=image:v2 # Update the image of the application container of the deployment
kubectl get deployments # list all deployments
kubectl describe deployments # return all informations about created deployments
kubectl describe deployments <deployment-name> # return all informations about deployment
kubectl get deployments -o wide # return informations about deployments with more details
kubectl delete deployment <deployment-name> # delete the deployment
```

For manage states of deployments is possible use the command **kubectl rollout**:

```shell
kubectl rollout history deployment/frontend                      # Check the history of deployments including the revision
kubectl rollout undo deployment/frontend                         # Rollback to the previous deployment
kubectl rollout undo deployment/frontend --to-revision=2         # Rollback to a specific revision
kubectl rollout status -w deployment/frontend                    # Watch rolling update status of "frontend" deployment until completion
kubectl rollout restart deployment/frontend                      # Rolling restart of the "frontend" deployment
```

## Network

In the kubernates by default the pods of the cluster has a unique IP and by default the pods can comunicate between each
other without declare links for this.

### [Services](https://kubernetes.io/docs/concepts/services-networking/service/)

For allow external access for the pods is necessary create a kubernates service. This resource is used to define wich
ports is opened to external access. Bellows has a example of a service definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: image-processing
  labels:
    app: myapp
spec:
  type: ClusterIP
  selector:
    tier: backend # label of pods
  ports:
    - port: 80
      targetPort: 8080
```

The kubernate possibilite create
many [types of services](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types):

**ClusterIP:** expose the service in an internal ip of the cluster. Is util for comunication beteween services and this
is default type.

```shell
spec:
  type: ClusterIP # Optional field (default)
  clusterIP: 10.10.0.1 # within service cluster ip range
  ports:
  - port: 80
    targetPort: 8080
```

- **NodePort:** extends the ClusterIP and expose a port os your pods for external access. By default automatically is
  created a ClusterIP for you cluster.

```shell
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 8080
```

- **LoadBalancer:** provision external access through a load balancer for the service in some cloud providers. In the cloud providers that not
  support load balancer is necessary use a external load balancer.

```shell
spec:
  type: LoadBalancer
  ports:
    - name: http
      port: 80
      targetPort: 8080
```

## Utils links:

- [Great basic kubernetes course](https://www.udemy.com/course/learn-kubernetes)
- [Architecture](https://kubernetes.io/docs/concepts/architecture/)
- [Components](https://kubernetes.io/docs/concepts/overview/components/#kubelet)
- [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Replicasets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Network](https://kubernetes.io/docs/concepts/services-networking/)
  - [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
  - [Services Types](https://www.baeldung.com/ops/kubernetes-service-types)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [kubectl Commands](https://kubernetes.io/docs/reference/kubectl/kubectl-cmds/)

## Next steps

- [Storage](https://kubernetes.io/docs/concepts/storage/)
- [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)
- [Resource Management for Pods and Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Diference between kubectl apply and create](https://www.baeldung.com/ops/kubectl-apply-create)