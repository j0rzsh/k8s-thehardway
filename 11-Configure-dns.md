# CONFIGURE DNS

## Install coredns

```bash
kubectl apply -f https://storage.googleapis.com/kubernetes-the-hard-way/coredns-1.7.0.yaml
```

```bash
kubectl run busybox --image=busybox:1.28 --command -- sleep 3600
kubectl get pods -l run=busybox
```

```bash
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")
```

```bash
kubectl exec -ti $POD_NAME -- nslookup kubernetes
```

After testing, delete busybox pod.