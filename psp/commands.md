## Security Context
- Create namespace: demo-psp
```
kubectl create ns demo-psp
```
- Set default namespace: 
```
kubens demo-psp
```

#### Privileged Container
By default, all containers run as root user
```
kubectl apply -f utils-privileged.yaml

apt-get update              #  Download binaries from internet
ps aux                      # see all processes
fallocate -l 1G 1Gbfile     # file creation in volume/node
```

#### Restricted Container

* readOnlyRootFilesystem: true
Other than volume mounts, make the container filesytem readonly. 
```
docker run --name docker-nginx -d -p 80:80 nginx
docker diff <containerId>   # Show the files/folders writable by conatiner
```

- Apply restricted container 
```
kubectl apply -f utils-restricted.yaml
```

#### HostPath
Hostpath mounts the folders from node
```
kubectl apply -f hostpath.yaml
```

## PSP
- defining set of such security context conditions a pod or container should have to run in the cluster
- it is a cluster level resource once created, admission controller for PSP validates requests to create and update Pods on your cluster

#### Enable PSP
- optional admission controller
- enabling it without any PSP, will block creating/updating any pods
- atleast 1 PSP should allow the pod creation/updation
- create the required PSPs first and then enable it
```
--enable-admission-controller=...,PodSecurityPolicy,...
```

#### Enable PSP In GKE:
```
gcloud beta container clusters update geekday-gke --enable-pod-security-policy

# Show psp config in cluster config
gcloud beta container clusters describe geekday-gke
```

### Demo
```
# Deploy any pod without any PSP
kubectl apply -f privileged.yaml

## Create privileged psp
kubectl apply -f 200-privileged-psp.yaml

# Deploy privileged pod with privileged psp
kubectl apply -f privileged.yaml

## Create restricted psp
kubectl delete -f 200-privileged-psp.yaml 
kubectl apply -f 100-restricted-psp.yaml

# Deploy privileged pod with restricted psp
kubectl apply -f privileged.yaml

## Deploy hostpath and check error in pod creation by describing relicaset
kubectl apply -f psp/hostpath.yaml
kubectl describe rs -l run=hostpath
```
