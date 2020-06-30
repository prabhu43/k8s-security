# Apply Security Context to Your Pods and Containers

- defines privilege and access control settings for a Pod or Container 

- If a container runs as root, it can allow to download malicious softwares and do any attack on other pods and nodes

## runAsUser: 1000 or runAsNonRoot
https://nvd.nist.gov/vuln/detail/CVE-2019-5736


## AllowPrivilegeEscalation: Controls whether a process can gain more privileges than its parent process.
is true always when the container is: 1) run as Privileged OR 2) has CAP_SYS_ADMIN.

## Privileged containers can allow almost completely unrestricted host access
Privileged containers share namespaces with the host system, forgo cgroup restrictions, and do not offer any security. They should be used exclusively as a bundling and distribution mechanism for the code in the container, and not for isolation.
https://kubesec.io/basics/containers-securitycontext-privileged-true/


## readOnlyRootFilesystem: true
docker run --name docker-nginx -p 80:80 nginx
- use docker diff
- use volumes
# LinuxCapabilities:

securityContext:
  capabilities:
    drop:
      - all
    add:
      - NET_BIND_SERVICE

https://opensource.com/business/15/3/docker-security-tuning

## Seccomp: Filter a process’s system calls.
Seccomp is a system call filtering facility in the Linux kernel which lets applications define limits on system calls they may make, and what should happen when system calls are made. Seccomp is used to reduce the attack surface available to applications


## SELinux Security Enhanced Linux (SELinux): Objects are assigned security labels.
## AppArmour: : Use program profiles to restrict the capabilities of individual programs.

