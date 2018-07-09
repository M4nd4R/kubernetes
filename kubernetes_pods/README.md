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


