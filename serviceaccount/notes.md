- Show default serviceaccount and its permissions
```
kubectl access-matrix --sa system:serviceaccount:default:default
```

- Exec into utils pod and show default serviceaccount mounted by default
```
KUBE_TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
curl -sSk -H "Authorization: Bearer $KUBE_TOKEN" \
      https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORT/api/v1/namespaces/default/pods/$HOSTNAME
curl -sSk -H "Authorization: Bearer $KUBE_TOKEN" \
      https://kubernetes.default.svc/api/v1/

```

- 