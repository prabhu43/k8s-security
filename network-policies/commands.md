## Demo when no NetworkPolicy exists
- Create namespace: demo-nw-policy
```
kubectl create ns demo-nw-policy
```

- Set default namespace: 
```
kubens demo-nw-policy
```

kubectl apply -f .

- Show guestbook app and its pods

- Open guestbook app in browser and enter some messages
```
kubectl port-forward service/guestbook-ui 8010:80
```

- Exec into utils pod and Connect to redis-master and put some messages
```
redis-cli -h redis-master
Show all keys: KEYS *
Show messages: GET messages
Update messages: SET messages "You are HACKED!!!"
```
- Show the messages changed in guestbook UI in browser

- Also Connect to redis-slave from another utils pod and show reading messages

## Apply network policy yaml
```
kubectl apply -f network-policies/guestbook-redis-allow-only-frontend.yaml
kubectl describe networkpolicy guestbook-redis-allow-only-frontend
```

- Now again try to connect to redis-master and redis-slave