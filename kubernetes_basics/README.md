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

Listing pods

```
# kubectl get pods
```

