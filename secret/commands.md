## Create namespace: demo-secret
```
kubectl create ns demo-secret
```

## Set default namespace: 
```
kubens demo-secret
```

## Create secret
```
kubectl create secret generic prod-secret --from-literal=username=produser --from-literal=password=strongpassword
```

## Fetch secret from etcd after SSH into master machine
```
ETCDCTL_API=3 etcdctl --endpoints "https://127.0.0.1:2379" --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key --cacert /etc/kubernetes/pki/etcd/ca.crt get /registry/secrets/demo-secret/prod-secret | hexdump -C
```

## Set EncryptionConfig for kube-apiserver

- Create encryption key
```
head -c 32 /dev/urandom | base64 -i - | pbcopy
```

- Paste the encryption key in this yaml and place this yaml in `/etc/kubernetes/encryption.yaml` in master node
```
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
    - secrets
    providers:
    - aescbc:
        keys:
        - name: key1
          secret: <BASE64-ENCODED-SECRET>
    - identity: {}
```

-  Edit the /etc/kubernetes/manifests/kube-apiserver.yaml as follows

-  Add this argument to kube-apiserver
```
--experimental-encryption-provider-config=/etc/kubernetes/encryption.yaml
```

- Add this in volumes
```
- hostPath:
      path: /etc/kubernetes/encryption.yaml
      type: File
    name: encryption-config
```

- Add this in volumeMounts:
```
- mountPath: /etc/kubernetes/encryption.yaml
      name: encryption-config
      readOnly: true
```

## Create new secret
```
kubectl create secret generic new-prod-db-secret --from-literal=username=produser --from-literal=password=strongpassword
```

## Fetch new secret from etcd
```
ETCDCTL_API=3 etcdctl --endpoints "https://127.0.0.1:2379" --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key --cacert /etc/kubernetes/pki/etcd/ca.crt get /registry/secrets/demo-secret/new-prod-secret | hexdump -C
```

# What about existing secrets
```
ETCDCTL_API=3 etcdctl --endpoints "https://127.0.0.1:2379" --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key --cacert /etc/kubernetes/pki/etcd/ca.crt get /registry/secrets/demo-secret/prod-secret | hexdump -C
```

# Encrypt existing secrets
```
kubectl get secrets -o json | kubectl replace -f -
```