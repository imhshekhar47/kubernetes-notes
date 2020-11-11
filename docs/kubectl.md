# Kubectl

### Install
```bash
# Download
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"

# Make executable
chmod +x ./kubectl

# Move to /usr/local/bin/ so that it's availale in PATH
sudo mv ./kubectl /usr/local/bin/kubectl

# Check the version
kubectl version --client
```


### Usage
```bash
# Get cluster information
kubectl cluster-info

# Get nodes in the cluster
kubectl get nodes -o wide

# Get pods in the cluster
kubectl get pods

# Get the details of the pod
kubectl describe pod <pod-name> 

# Get logs of the pod
kubectl logs <pod-name>

# Access the terminal of the pod
kubectl exec -it <pod-name> -- /bin/bash
```