## What if kubelet is compromised

### Allow anonymous access and read-only port
By SSH into any worker node, change the kubelet config
```
vi /var/lib/kubelet/config.yaml
authentication.anonymous.enabled: true
readOnlyPort: 10255
```

> Note: This is only for demo purpose 

### Access kubelet from our machine
```
telnet nodeIP 10255
curl http://nodeIP:10255/pods
```


