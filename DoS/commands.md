## Resource Quota

### Create resource quota for max limit of 10GB memory
kubectl create -f demo-resourcequota.yaml

### Deploy a pod with memory limit of 2GB
kubectl create -f myapp-pod.yaml

### Check used resource in resourcequota
kubectl describe resourcequota demo-resourcequota

### Deploy a pod with memory limit of 9GB
kubectl create -f myapp-highmem-pod.yaml

- It fails as it exceeds resourcequota of 10GB memory (2+9=11)

### Deploy a pod without memory limit
kubectl create -f myapp-no-resources-pod.yaml

- It fails as memory limit is not specified


## LimitRange

### Create limitrange with default memory limit of 1GB
kubectl create -f default-requests-limits.yaml

### Deploy a pod without memory limit
kubectl create -f myapp-no-resources-pod.yaml

- Pod will be created with default memory limit of 1GB

### Check used resource in resourcequota
kubectl describe resourcequota demo-resourcequota