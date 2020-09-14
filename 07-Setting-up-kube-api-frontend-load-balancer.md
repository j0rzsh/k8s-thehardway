# SET UP KUBE API FRONTEND LOAD BALANCER

This configuration is made on loadbalancer instance

## Install and configure nginx

```bash
sudo apt-get install -y nginx
sudo systemctl enable nginx
sudo mkdir -p /etc/nginx/tcpconf.d
```

Then edit nginx.conf:

```bash
sudo vi /etc/nginx/nginx.conf
```

And add the following line at the end:

```bash
include /etc/nginx/tcpconf.d/*;
```

Having:

- controller1,172.31.21.122
- controller2,172.31.28.180

Set:

CONTROLLER1_IP=172.31.21.122
CONTROLLER2_IP=172.31.28.180

```bash
cat << EOF | sudo tee /etc/nginx/tcpconf.d/kubernetes.conf
stream {
  upstream kubernetes {
    server $CONTROLLER1_IP:6443;
    server $CONTROLLER2_IP:6443;
  }

  server {
    listen 6443;
    listen 443;
    proxy_pass kubernetes;
  }
}
EOF
```

```bash
sudo nginx -s reload
```

```bash
curl -k https://localhost:6443/version
```
