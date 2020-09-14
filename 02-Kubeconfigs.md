# KUBECONFIGS

1. Kubelet (one for each worker)
2. Kube-proxy
3. Kube-controller-manager
4. Kube-scheduler
5. Admin

## Generate Kubeconfigs

### Kubernetes API Server IP

KUBERNETES_ADDRESS=172.31.22.4

### The kubelet Kubernetes Configuration File

```bash
for instance in worker1 worker2; do
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_ADDRESS}:6443 \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-credentials system:node:${instance} \
    --client-certificate=${instance}.pem \
    --client-key=${instance}-key.pem \
    --embed-certs=true \
    --kubeconfig=${instance}.kubeconfig

  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:node:${instance} \
    --kubeconfig=${instance}.kubeconfig

  kubectl config use-context default --kubeconfig=${instance}.kubeconfig
done
```

### The kube-proxy Kubernetes Configuration File

```bash
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://${KUBERNETES_ADDRESS}:6443 \
    --kubeconfig=kube-proxy.kubeconfig
```

```bash
  kubectl config set-credentials system:kube-proxy \
    --client-certificate=kube-proxy.pem \
    --client-key=kube-proxy-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-proxy.kubeconfig
```

```bash
  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-proxy \
    --kubeconfig=kube-proxy.kubeconfig
```

```bash
  kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig
```

### The kube-controller-manager Kubernetes Configuration File

```bash
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-controller-manager.kubeconfig
```

```bash
  kubectl config set-credentials system:kube-controller-manager \
    --client-certificate=kube-controller-manager.pem \
    --client-key=kube-controller-manager-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-controller-manager.kubeconfig
```

```bash
  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-controller-manager \
    --kubeconfig=kube-controller-manager.kubeconfig
```

```bash
  kubectl config use-context default --kubeconfig=kube-controller-manager.kubeconfig
```

### The kube-scheduler Kubernetes Configuration File

```bash
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=kube-scheduler.kubeconfig
```

```bash
  kubectl config set-credentials system:kube-scheduler \
    --client-certificate=kube-scheduler.pem \
    --client-key=kube-scheduler-key.pem \
    --embed-certs=true \
    --kubeconfig=kube-scheduler.kubeconfig
```

```bash
  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=system:kube-scheduler \
    --kubeconfig=kube-scheduler.kubeconfig
```

```bash
  kubectl config use-context default --kubeconfig=kube-scheduler.kubeconfig
```

### The admin Kubernetes Configuration File

```bash
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://127.0.0.1:6443 \
    --kubeconfig=admin.kubeconfig
```

```bash
  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem \
    --embed-certs=true \
    --kubeconfig=admin.kubeconfig
```

```bash
  kubectl config set-context default \
    --cluster=kubernetes-the-hard-way \
    --user=admin \
    --kubeconfig=admin.kubeconfig
```

```bash
  kubectl config use-context default --kubeconfig=admin.kubeconfig
```

## Distribute the Kubernetes Configuration Files

### To Worker 1

- worker1.kubeconfig
- kube-proxy.kubeconfig

### To Worker 2

- worker2.kubeconfig
- kube-proxy.kubeconfig

### To Controller 1

- admin.kubeconfig
- kube-controller-manager.kubeconfig
- kube-scheduler.kubeconfig

### To Controller 2

- admin.kubeconfig
- kube-controller-manager.kubeconfig
- kube-scheduler.kubeconfig
