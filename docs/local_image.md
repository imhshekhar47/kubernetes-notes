# Local docker image with Kind cluster

Upload local image to kind cluster

```bash
kind load docker-image <image>
```

Check the list of images in kind cluster
```bash
docker exec -it <kind-node> crictl images 
```

Note: use `imagePullPolicy: IfNotPresent` and DO NOT use latest tag.