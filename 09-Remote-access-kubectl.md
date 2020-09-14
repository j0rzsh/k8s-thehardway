# CONFIGURING KUBECTL FOR REMOTE ACCESS

## SSH Tunnel against Load Balancer

In my local machine:

loadbalancer is configured in ~/.ssh/config

```bash
ssh -L 6443:localhost:6443 loadbalancer
```

## Set cluster in localhost as we have the ssh tunnel on

```bash
  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.pem \
    --embed-certs=true \
    --server=https://localhost:6443
```

```bash
  kubectl config set-credentials admin \
    --client-certificate=admin.pem \
    --client-key=admin-key.pem
```

```bash
  kubectl config set-context kubernetes-the-hard-way \
    --cluster=kubernetes-the-hard-way \
    --user=admin
```

```bash
  kubectl config use-context kubernetes-the-hard-way
```
