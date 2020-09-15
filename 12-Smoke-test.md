# SMOKE TEST

## Data Encryption

```bash
kubectl create secret generic kubernetes-the-hard-way \
  --from-literal="richard=claydermanensupianosincontrol"
```

```bash
sudo ETCDCTL_API=3 etcdctl get \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/kubernetes.pem \
  --key=/etc/etcd/kubernetes-key.pem\
  /registry/secrets/default/kubernetes-the-hard-way | hexdump -C
```

Verify the output specifies the format which tells that it is encrypted.

## Deployments

```bash
kubectl create deployment nginx --image=nginx
```

```bash
kubectl get pods -l app=nginx
```

## Port Forwarding

```bash
POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath="{.items[0].metadata.name}")
```

```bash
kubectl port-forward $POD_NAME 8080:80
```

In another terminal:

```bash
curl --head http://127.0.0.1:8080
```

## Logs

```bash
kubectl logs $POD_NAME
```

## Exec

```bash
kubectl exec -ti $POD_NAME -- nginx -v
```

## Services

```bash
kubectl expose deployment nginx --port 80 --type NodePort
```

```bash
NODE_PORT=$(kubectl get svc nginx \
  --output=jsonpath='{range .spec.ports[0]}{.nodePort}')
```

Log into one worker and execute:

```bash
curl -I localhost:$NODE_PORT
```
