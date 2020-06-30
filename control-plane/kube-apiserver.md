### Manifest path:
/etc/kubernetes/manifests/kube-apiserver.yaml

### kube-apiserver process
ps aux | grep kube-apiserver

### Path to container logs
/var/log/containers/
This folder contains logs of k8s control plane like kube-api-server, etcd, kube-controller-manager, kube-scheduler

### See default enabled admission controllers
k logs  kube-apiserver-k8s-master -n kube-system | head -n 20

