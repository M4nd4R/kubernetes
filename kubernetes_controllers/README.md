# KUBERNETES REPLICATION AND OTHER CONTROLLERS - PODS MANAGEMENT

### Liveness Probes

HTTP liveness probe

```
apiVersion: v1
kind: pod
metadata:
  name: kubia-liveness
spec:
  containers:
  - image: luksa/kubia-unhealthy
    name: kubia
    livenessProbe:
      httpGet:
        path: /
        port: 8080
```

