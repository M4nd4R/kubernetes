# KUBERNETES BASICS

Create a Project in GCP

```
gcloud init
  --> Follow the instructions
```

Create  a 3-node Kubernetes Cluster

```
# gcloud container clusters create kubia --num-nodes 3 --machine-type f1-micro
```

Listing cluster nodes

```
# kubectl get nodes
NAME                                   STATUS    ROLES     AGE       VERSION
gke-kubia-default-pool-c4f5eab5-49p3   Ready     <none>    5m        v1.9.7-gke.3
gke-kubia-default-pool-c4f5eab5-s980   Ready     <none>    5m        v1.9.7-gke.3
gke-kubia-default-pool-c4f5eab5-tlp6   Ready     <none>    5m        v1.9.7-gke.3
```

Login to a cluster node

```
# gcloud compute ssh gke-kubia-default-pool-c4f5eab5-49p3
```

Additonal details of a node

```
# kubectl describe node gke-kubia-default-pool-c4f5eab5-49p3
```

Creating pods through ReplicationControllers

```
# kubectl run kubia --image=m4nd4r/kubia --port=8080 --generator=run/v1
```

Listing pods

```
# kubectl get pods
NAME          READY     STATUS    RESTARTS   AGE
kubia-rpzwf   1/1       Running   0          14h
```

Creating a Service object

```
# kubectl expose rc kubia --type=LoadBalancer --name kubia-http
service "kubia-http" exposed
```

Listing services

```
# kubectl get services
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.27.240.1    <none>        443/TCP          15h
kubia-http   LoadBalancer   10.27.251.36   <pending>     8080:31245/TCP   33s

OR

# kubectl get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
kubernetes   ClusterIP      10.27.240.1    <none>          443/TCP          15h
kubia-http   LoadBalancer   10.27.251.36   35.200.187.47   8080:31245/TCP   59s
```

Listing ReplicationControllers

```
# kubectl get rc
NAME      DESIRED   CURRENT   READY     AGE
kubia     1         1         1         14h
```

Scaling replica count for the ReplicationController

```
# kubectl scale rc kubia --replicas=3
replicationcontroller "kubia" scaled

# kubectl get rc
NAME      DESIRED   CURRENT   READY     AGE
kubia     3         3         3         14h

# kubectl get pods
NAME          READY     STATUS    RESTARTS   AGE
kubia-dhk46   1/1       Running   0          1m
kubia-rpzwf   1/1       Running   0          15h
kubia-vpxz9   1/1       Running   0          1m
```





