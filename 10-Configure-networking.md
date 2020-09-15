# CONFIGURE NETWORKING

## Set ipv4 forwarding to 1 on worker nodes

On worker1 and worker2

```bash
sudo sysctl net.ipv4.conf.all.forwarding=1
echo "net.ipv4.conf.all.forwarding=1" | sudo tee -a /etc/sysctl.conf
```

## Install weave net plugin

I do not understand this but i needed to do it:

```bash
sudo mkdir /run/systemd/resolve/
sudo ln -s /etc/resolv.conf /run/systemd/resolve/resolv.conf
```

Then:

```bash
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')&env.IPALLOC_RANGE=10.200.0.0/16"
```


## Create pods for testing connections

```bash
cat << EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      run: nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF
```

```bash
kubectl expose deployment/nginx
```

```bash
kubectl run busybox --image=radial/busyboxplus:curl --command -- sleep 3600
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")
```

```bash
kubectl exec $POD_NAME -- curl <first nginx pod IP address>
kubectl exec $POD_NAME -- curl <second nginx pod IP address>
```

```bash
kubectl get svc
```

```bash
kubectl exec $POD_NAME -- curl <nginx service IP address>
```

After testing, delete nginx and busybox pods.
