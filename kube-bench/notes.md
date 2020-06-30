## Audit by Kube-bench
- Intro to CIS benchmark and its sections

- Install kube-bench in our machine
https://github.com/aquasecurity/kube-bench#installation

- Run kube-bench using pods
```

export KUBEBENCH_PATH="$GOPATH/src/github.com/aquasecurity/kube-bench"
kubectl apply -f $KUBEBENCH_PATH/job.yaml
kubectl apply -f $KUBEBENCH_PATH/job-master.yaml
kubectl apply -f $KUBEBENCH_PATH/job-node.yaml

kubectl logs <all pods>
```
- Run kube-bench from master & worker nodes
```
docker run --rm -v `pwd`:/host aquasec/kube-bench:latest install
./kube-bench
./kube-bench master
./kube-bench node
```

- Check some warnings or fails and its recommendations

## References

- https://github.com/aquasecurity/kube-bench