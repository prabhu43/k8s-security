# Network Policies
One important configuration that demands attention from a security perspective is the network policies feature.

Network policies specify how groups of pods are allowed to communicate with each other and other network endpoints. You can think of them as the Kubernetes equivalent of a firewall.
 In other words, it creates firewalls between pods running on a Kubernetes cluster.

By default, Kubernetes does not restrict traffic between pods running inside the cluster. This means any pod can connect to any other pod as there are no firewalls controlling the intra-cluster traffic.

This can give way for intruders who got access to a malicious pod can reach out to all pods from that.

<Demo>

Enforce policies for both connection:
* Ingress
* Egress

Network Policies give you a way to declaratively configure which pods are allowed to connect to each other. These policies can be detailed: you can specify which namespaces are allowed to communicate, or more specifically you can choose which port numbers to enforce each policy on.

The Network Policy implementation is not a Kubernetes core functionality. Although you can submit a NetworkPolicy object to the Kubernetes master, if your network plugin does not implement network policy, it will not be enforced.

Use a network plugin that supports network policies
Example: Canal, Calico, Cilium, Kube-router, Romana and Weave Net

A network policy has 2 parts:
  - target pods: podSelector: selects a group of (zero or more) pods
  - ingress/egress rules: list of allowed connections

### Segmenting traffic => Defense in depth
  Labels can then be used to mimic traditional segmented networks often used to isolate layers in a multi-tier application: You might identify your front-end and back-end pods by a specific “segment” label, for example. Policies control traffic between those segments and even traffic to or from external sources.

Rules:
* pod with 0 n/w policies => `allows all` network connections to and from
* pod has n/w policies: traffic restricted
  * atleast 1 n/w policy required to allow traffic (All rules are UNIONed)(A connection to or from a pod is allowed if it is permitted by at least one of the network policies that apply to the pod.)
* n/w policies are namespaced

Recommendation:
First block all network connections, then start whitelisting each apps  

# Deny all ingress traffic for all pods in a namespace
```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: deny-all
spec:
  podSelector: {}
  ingress: {}
```
*Note: Since network policies are namespaced resources, you will need to create this policy for each namespace. You can do so by running kubectl -n  create -f  for each namespace.*

Lets take an example app having frontend, backend and database
`Show diagram`
# For database, Deny ingress from any pods except backend (podselector)
```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: db-allow-only-backend
spec:
  podSelector: 
    matchLabels:
      app: guestbook-db
      tier: db
  ingress:
  - from:
      podSelector: 
        matchLabels:
          app: guestbook-api
          tier: backend
    ports: # Restricting port numbers
    - port: 5432
      protocol: TCP
```

# Allow traffic from all pods in other namespace(namespace)
```yaml
kind: Namespace
apiVersion: v1
metadata:
  name: monitoring
  labels:
    team: monitoring
```

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: prod-storage
  name: allow-monitoring-namespace
spec:
  podSelector: {}
  ingress:
  - from:
      ipBlock:
        cidr: 10.72.0.0/16
        except: 10.72.10.0/8

```

# allow 10.56.X.X, but deny 10.56.10.X (ipBlock)
```yaml
from:
- ipBlock:
    cidr: 10.72.0.0/16
    except: 10.72.10.0/8
```

### Extra notes:
- Defence in depth: multiple layers of defense that protect Google’s network from external attacks
- sub 1ms overhead
- Update iptables of nodes
