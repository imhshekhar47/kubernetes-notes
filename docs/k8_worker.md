# K8 Worker Node

### Container Runtime
The worker node must have a container runtime (docker)

### Kubelet
Interface between container runtime and node itself.
- Responsible for recieveing configuration from API Server, starting up container and assigning resources (cpu, storage) from node to container.


### Kube Proxy
- Has network forward intelligence to route traffic.
- Smartly route traffic to local (node) service instances to avooid network overhead. 