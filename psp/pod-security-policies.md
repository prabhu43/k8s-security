# Pod Security Policy

- cluster-level resource that controls security sensitive aspects of the pod specification. The PodSecurityPolicy objects define a set of conditions that a pod must run with in order to be accepted into the system, as well as defaults for the related fields

- helps to assign secure defaults 

- admission controller resource you create that validates requests to create and update Pods on your cluster. The PodSecurityPolicy defines a set of conditions that Pods must meet to be accepted by the cluster; when a request to create or update a Pod does not meet the conditions in the PodSecurityPolicy, that request is rejected and an error is returned.

- implemented as an optional (but recommended) admission controller
- enforced by enabling the admission controller `--enable-admission-plugins=PodSecurityPolicy,...`
- if PodSecurityPolicy is enabled but without any policies defined, creating any pods in kubernetes cluster will be blocked. 
`kubectl get psp`

To use PodSecurityPolicy, you must first create and define policies that new and updated Pods must meet. Then, you must enable the PodSecurityPolicy admission controller, which validates requests to create and update Pods against the defined policies.

### Authorise PSP via RBAC:

* First, a Role or ClusterRole needs to grant access to use the desired policies
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: <role name>
rules:
- apiGroups: ['policy']
  resources: ['podsecuritypolicies']
  verbs:     ['use']
  resourceNames:
  - <list of policies to authorize>
```

* Then, (Cluster)Role is bound to the authorized user(s)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: <binding name>
roleRef:
  kind: ClusterRole
  name: <role name>
  apiGroup: rbac.authorization.k8s.io
subjects:
# Authorize specific service accounts:
- kind: ServiceAccount
  name: <authorized service account name>
  namespace: <authorized pod namespace>
# Authorize specific users (not recommended):
- kind: User
  apiGroup: rbac.authorization.k8s.io
  name: <authorized user name>
```

## Policy order:
- Non mutating - any order
- Mutating - alphabetical order
hence use names like 10-privileged, 20-restricted,30-some-psp

### Read-only filesystem
allows write only to given volume. writing to /tmp is not possible.
demo: use docker diff to find the path needs to be RW for app to work. excluding that folder, make all others readonly

### Privileged mode for pod containers
Any container in a Pod can enable privileged mode, using the privileged flag on the security context of the container spec. This is useful for containers that want to use Linux capabilities like manipulating the network stack and accessing devices. Processes within the container get almost the same privileges that are available to processes outside a container. With privileged mode, it should be easier to write network and volume plugins as separate Pods that don’t need to be compiled into the kubelet.

### Linux capabilities
- drop all root user capabilities. give only the permissions required by the app
- run as normal user, not root
- allowPrivilegeEscalation: false

### sandboxed pods
- gvisor, kata containers

### AppArmor/SELinux: linux extensions for security modeling

### seccomp


### Examples: https://github.com/jjo/jjo-talks/tree/master/2019/cncf-webinar-kube-psps/examples

apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  privileged: false
  hostPID: false
  hostIPC: false


## Ref:

https://medium.com/coryodaniel/kubernetes-assigning-pod-security-policies-with-rbac-2ad2e847c754