# SETTING UP ETCD

For controller1 and controller2.

## Download binary and install it on the correct directory

```bash
  wget -q --show-progress --https-only --timestamping \
    "https://github.com/etcd-io/etcd/releases/download/v3.4.10/etcd-v3.4.10-linux-amd64.tar.gz"
  tar -xvf etcd-v3.4.10-linux-amd64.tar.gz
  sudo mv etcd-v3.4.10-linux-amd64/etcd* /usr/local/bin/
```

## Create needed directories and copy ca and kubernetes keys into /etc/etcd

```bash
  sudo mkdir -p /etc/etcd /var/lib/etcd
  sudo chmod 700 /var/lib/etcd
  sudo cp ca.pem kubernetes-key.pem kubernetes.pem /etc/etcd/
```

## Set ETCD_NAME and INTERNAL_IP variables

```bash
ETCD_NAME=$(hostname -s)  # Each node has to be named uniquely
INTERNAL_IP=$(curl http://169.254.169.254/latest/meta-data/local-ipv4)
```

## Set INITIAL_CLUSTER variable

- controller1,172.31.21.122
- controller2,172.31.28.180

```bash
INITIAL_CLUSTER=controller1=https://172.31.21.122:2380,controller2=https://172.31.28.180:2380
```

## Configure etcd service and start it

```bash
cat << EOF | sudo tee /etc/systemd/system/etcd.service
[Unit]
Description=etcd
Documentation=https://github.com/coreos


[Service]
ExecStart=/usr/local/bin/etcd \\
  --name ${ETCD_NAME} \\
  --cert-file=/etc/etcd/kubernetes.pem \\
  --key-file=/etc/etcd/kubernetes-key.pem \\
  --peer-cert-file=/etc/etcd/kubernetes.pem \\
  --peer-key-file=/etc/etcd/kubernetes-key.pem \\
  --trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-trusted-ca-file=/etc/etcd/ca.pem \\
  --peer-client-cert-auth \\
  --client-cert-auth \\
  --initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-peer-urls https://${INTERNAL_IP}:2380 \\
  --listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
  --advertise-client-urls https://${INTERNAL_IP}:2379 \\
  --initial-cluster-token etcd-cluster-0 \\
  --initial-cluster ${INITIAL_CLUSTER} \\
  --initial-cluster-state new \\
  --data-dir=/var/lib/etcd
Restart=on-failure
RestartSec=5


[Install]
WantedBy=multi-user.target
EOF
```

```bash
  sudo systemctl daemon-reload
  sudo systemctl enable etcd
  sudo systemctl start etcd
```

Verify that everything is set up correctly:

```bash
sudo ETCDCTL_API=3 etcdctl member list \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/kubernetes.pem \
  --key=/etc/etcd/kubernetes-key.pem
```
