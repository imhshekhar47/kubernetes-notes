# Tekton
![TEKTON](https://tekton.dev/images/tekton-horizontal-color.png)
[Tekton](https://tekton.dev/docs/) is a cloud native continuous integration and delivery (CI/CD) solution. It allows developers to build, test, and deploy across cloud providers and on-premise systems. 

# Installation

### Prerequisite: 
- You must have a kubernetes cluster and accessible via kubectl.


## Install tekton CRDs
```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

## Install tekton-triggers
```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
```

## Install dashboard
```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/dashboard/latest/tekton-dashboard-release.yaml
```

### Accessing dashboard
```bash
kubectl proxy
```
Then brows [dashboard](http://localhost:8001/api/v1/namespaces/tekton-pipelines/services/tekton-dashboard:http/proxy/)



## Install cli tool
```bash
# Get the tar.xz
curl -LO https://github.com/tektoncd/cli/releases/download/v0.13.1/tkn_0.13.1_Linux_x86_64.tar.gz
# Extract tkn to your PATH (e.g. /usr/local/bin)
sudo tar xvzf tkn_0.13.1_Linux_x86_64.tar.gz -C /usr/local/bin/ tkn
```

# Getting started
### 1. Create hello task
```bash
cat << EOF | kubectl apply -f -
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: hello
spec:
  steps:
  - name: hello
    image: busybox
    command:
    - echo
    args:
    - "hello tekton!!"
EOF
```

### 2. Run task 
This creates a simple tasks `hello`. \
You can run this task using tkn cli as shown belwo
```bash
tkn taskrun start hello
```

