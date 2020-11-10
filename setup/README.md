# Setup

We will be using kind to create a multinode cluster locally on a Linux systsem.

## Install KinD
[kind](https://kind.sigs.k8s.io/) is a tool for running local Kubernetes clusters using Docker container “nodes”.


## 1. Setup multinode cluster
```bash
kind create cluster --name kind --config ./multinode-cluster.yml
```

## 2. Setup metallb
[MetalLB](https://metallb.universe.tf/) is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.
### 2.1 Create metallb resources
```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.4/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.9.4/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

### 2.2 Layer 2 Configuration
Check the node network `kubectl get node -o wide` and find out the corresponding subntet range from `ip addr`.

For example
```bash
kubectl get node -o wide
#output
NAME                 STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                                     KERNEL-VERSION     CONTAINER-RUNTIME
kind-control-plane   Ready    master   30m   v1.19.1   172.18.0.2    <none>        Ubuntu Groovy Gorilla (development branch)   5.4.0-52-generic   containerd://1.4.0
kind-worker          Ready    <none>   29m   v1.19.1   172.18.0.4    <none>        Ubuntu Groovy Gorilla (development branch)   5.4.0-52-generic   containerd://1.4.0
kind-worker2         Ready    <none>   29m   v1.19.1   172.18.0.3    <none>        Ubuntu Groovy Gorilla (development branch)   5.4.0-52-generic   containerd://1.4.0
```
We see all the nodes are in `172.18.X.X` range and that is in which in `ip addr` appears under network `br-xxxxxx` with subnet `172.18.0.1/16`. 
This suggest that usable rage of IP is `172.18.0.1-172.18.255.255`. Thus select subset of ips for loadbalancing `172.18.0.1-172.18.255.250` and apply below configuration. 

```bash
cat << EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.18.255.1-172.18.255.250
EOF
```
We are done. Now you can have loadbalancers in your clusters

## 3. Ingress setup (Nginx)
You must have helm installed for this.
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install ingress-nginx/ingress-nginx --name my-nginx set rbac.create=true
```
Now upon `kubectl get services` you should see 
```bash
NAME                                                    TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE
service/my-nginx-ingress-nginx-controller               LoadBalancer   xx.xx.xx.xx     <EXTERNAL-IP>    80:31465/TCP,443:31299/TCP   101m
```
You can use this `EXTERNAL-IP` as as dns name in /etc/hosts say localhost.kube
```bash
...
EXTERNAL-IP   localhost.kube
EXTERNAL-IP   foo.localhost.kube
EXTERNAL-IP   foo.localhost.kube
``` 
Thus any call made in browser to eiher of `localhost.kube`, `foo.localhost.kube` or `foo.localhost.kube` will be redirected to ingress controller.
Therefore ingress controller then will be able to redirec to relevent service.

Test the setup
```bash
kubectl apply -f ./verify-setup.yml
```
Then try calling foo and bar services by ingress paths

```bash
curl localhost.kube/foo
# should show 
<h1>Welcome to <span style="color:#005588">FOO</span></h1>
```

```bash
curl localhost.kube/bar
# should show
<h1>Welcom to <span style="color:#008800">BAR</span></h1>
```
You can try the same from the browser as well.

