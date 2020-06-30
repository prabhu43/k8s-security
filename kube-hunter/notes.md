## Attack surfaces
- Outside cluster
- Nodes in cluster
- Pods in cluster
- Control plane

## Identify Security Weaknesses using kube-hunter

- Install kube-hunter in our machine
https://github.com/aquasecurity/kube-hunter#prerequisites

- Run kube-hunter from outside cluster(from our machine):
```
kube-hunter 
(Choose Remote Scanning)
```

- Run kube-hunter from inside master & worker nodes
```
docker run -it --rm --network host aquasec/kube-hunter
(Choose Interface scanning)
```

- Run kube-hunter inside pod
```
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-hunter/master/job.yaml
kubectl logs <pod>
```

## References
- https://kube-hunter.aquasec.com/