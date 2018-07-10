# PODS: RUNNING CONTAINERS IN KUBERNETES

### Creating Pods from YAML / JSON

YAML descriptor of a pod

```
# kubectl get pods kubia-dhk46 -o yaml/json
````

Create a YAML descriptor for a pod

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual
spec:
  containers:
  - image: m4nd4r/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```

Create the pod using descriptor

```
# kubectl create -f <filename>
```
```
# kubectl get pods
NAME           READY     STATUS    RESTARTS   AGE
kubia-dhk46    1/1       Running   0          1d
kubia-manual   1/1       Running   0          17s
kubia-rpzwf    1/1       Running   0          1d
kubia-vpxz9    1/1       Running   0          1d
```

View logs of the pod

```
# kubectl logs <pod-name>
```

For a pod containing multiple containers

```
# kubectl logs <pod-name> -c <container-name>
```

Forward a port on local machine to a port on a pod

```
# kubectl port-forward <pod-name> <local-port>:<pod-port>

# kubectl port-forward kubia-manual 9000:8080
Forwarding from 127.0.0.1:9000 -> 8080
Forwarding from [::1]:9000 -> 8080
```

### Organizing pods with labels

Adding labels while creating pods

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual-labels
  labels:
    creation_method: manual
    env: prod
spec:
  containers:
  - image: m4nd4r/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```
```
# kubectl get pods --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
kubia-dhk46           1/1       Running   0          1d        run=kubia
kubia-manual          1/1       Running   0          48m       <none>
kubia-manual-labels   1/1       Running   0          1m        creation_method=manual,env=prod
kubia-rpzwf           1/1       Running   0          1d        run=kubia
kubia-vpxz9           1/1       Running   0          1d        run=kubia
```

Show labels in column

```
# kubectl get pods -L creation_method,env
NAME                  READY     STATUS    RESTARTS   AGE       CREATION_METHOD   ENV
kubia-dhk46           1/1       Running   0          1d                          
kubia-manual          1/1       Running   0          50m                         
kubia-manual-labels   1/1       Running   0          2m        manual            prod
kubia-rpzwf           1/1       Running   0          1d                          
kubia-vpxz9           1/1       Running   0          1d
```

Adding labels

```
# kubectl label pods kubia-manual creation_method=manual
pod "kubia-manual" labeled
```

Modifying labels

```
# kubectl label pods kubia-manual-labels env=debug --overwrite
pod "kubia-manual-labels" labeled
```
```
# kubectl get pods -L creation_method,env
NAME                  READY     STATUS    RESTARTS   AGE       CREATION_METHOD   ENV
kubia-dhk46           1/1       Running   0          1d                          
kubia-manual          1/1       Running   0          52m       manual            
kubia-manual-labels   1/1       Running   0          5m        manual            debug
kubia-rpzwf           1/1       Running   0          1d                          
kubia-vpxz9           1/1       Running   0          1d                          
```

Listing pods with label selectors

```
-> Contains a label with a value
# kubectl get pods -l creation_method=manual
NAME                  READY     STATUS    RESTARTS   AGE
kubia-manual          1/1       Running   0          11h
kubia-manual-labels   1/1       Running   0          10h

-> Containers a label
# kubectl get pods -l env
NAME                  READY     STATUS    RESTARTS   AGE
kubia-manual-labels   1/1       Running   0          10h

-> Does not contain a label
# kubectl get pods -l '!env'

-> Multiple labels
# kubectl get pods -l creation_method=manual,env=debug
```

### Introducing Namespaces

List namespaces

```
# kubectl get ns
```

Lising pods from a namespace

```
# kubectl get namespaces
NAME          STATUS    AGE
default       Active    2d
kube-public   Active    2d
kube-system   Active    2d

# kubectl get pods --namespace kube-system

OR

# kubectl get pods -n kube-system
NAME                                              READY     STATUS    RESTARTS   AGE
event-exporter-v0.1.9-5c8fb98cdb-d4x2d            2/2       Running   0          2d
fluentd-gcp-v2.0.17-7cs8p                         2/2       Running   0          2d
```

Creating a namespace using YAML

```
# cat custom-namespace-yaml.yaml 
apiVersion: v1
kind: Namespace
metadata:
  name: custom-namespace-yaml

# kubectl create -f custom-namespace-yaml.yaml 
namespace "custom-namespace-yaml" created

# kubectl get ns
NAME                    STATUS    AGE
custom-namespace-yaml   Active    7s
default                 Active    2d
kube-public             Active    2d
kube-system             Active    2d
```

Creating a namespace w/o YAML

```
# kubectl create namespace <ns-name>
```
```
# kubectl create namespace custom-namespace-nonyaml
namespace "custom-namespace-nonyaml" created
```

Creating pods in the namespace - using YAML

```
# cat kubia-manual-yaml.yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual
  namespace: custom-namespace-yaml
spec:
  containers:
  - image: m4nd4r/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP

# kubectl create -f kubia-manual-yaml.yaml 
pod "kubia-manual" created

# kubectl get pods -n custom-namespace-yaml
NAME           READY     STATUS    RESTARTS   AGE
kubia-manual   1/1       Running   0          14s
```

Creating pods in namespace - w/o YAML

```
# kubectl create -f kubia-manual.yaml -n custom-namespace-nonyaml
pod "kubia-manual" created

# kubectl get pods -n custom-namespace-nonyaml
NAME           READY     STATUS    RESTARTS   AGE
kubia-manual   1/1       Running   0          14s
```

### Deleting Pods

Deleting pods by name

```
# kubectl delete pods kubia-manual-labels
pod "kubia-manual-labels" deleted
```

Deleting multiple pods

```
# kubectl delete po <pod1> <pod2> ...
```

Deleting pods with label selector

```
# kubectl delete pods -l creation_method=manual
pod "kubia-manual" deleted
```

Deleting pods by deleting whole namespace

```
# kubectl delete ns custom-namespace-yaml
namespace "custom-namespace-yaml" deleted
```

Delete all pods in current namespace

```
-> This doesn't delete pods created by ReplicationControllers. You need to delete RCs for that
# kubectl delete pods --all
```

Delete all resources in current namespace (pods, services, RCs, etc.)

```
# kubectl delete all --all

all : all types of resources
--all : all instances
```
