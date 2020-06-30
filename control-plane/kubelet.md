## What if kubelet is compromised

### Allow anonymous access and read-only port
By SSH into any worker node, change the kubelet config
- sudo systemctl status kubelet -> find kubelet config path
- vi /var/lib/kubelet/config.yaml
```
authentication.anonymous.enabled: true
readOnlyPort: 10255
```
- Restart kubelet: sudo systemctl restart kubelet

> Note: This is only for demo purpose 

### Access kubelet from our machine
```
telnet nodeIP 10255
curl http://nodeIP:10255/pods
```

### Place malicious pod in static pod path as mentioned on kubelet config
- Place stress.pod.yaml in `staticPodPath: /etc/kubernetes/manifests`

