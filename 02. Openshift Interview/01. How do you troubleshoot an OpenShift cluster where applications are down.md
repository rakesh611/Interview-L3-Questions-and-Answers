1. How do you troubleshoot an OpenShift cluster where applications are down?

## 1. Purpose

This runbook provides a structured L1/L2/L3 troubleshooting process for an OpenShift cluster where one or more applications are down.

The key principle is:

> **Start from the user-facing symptom and move down the stack: Route → Service → Endpoint/Pod → Application → Node → Network → Storage → Cluster Operators → Control Plane.**

Do not restart pods randomly. First identify **where the failure occurs** and collect evidence.

---

# 2. High-Level Troubleshooting Flow

```text
User cannot access application
          |
          v
       Route
          |
          v
      Ingress
          |
          v
       Service
          |
          v
 Endpoints / EndpointSlices
          |
          v
         Pod
          |
          v
 Application logs / probes
          |
          v
 Deployment / ReplicaSet / StatefulSet
          |
          v
 Node health
          |
          v
 Network / DNS / OVN
          |
          v
 Storage / PVC
          |
          v
 Cluster Operators
          |
          v
 API Server / etcd / Control Plane
```

---

# 3. Step 1 — Confirm the Scope of the Incident

First determine whether:

- One application is down.
- One namespace is affected.
- Multiple applications are affected.
- All applications are affected.
- The OpenShift console is also affected.
- The API server is healthy.
- The problem started after a deployment, node change, upgrade, certificate change, network change, or storage change.

Check cluster access:

```bash
oc whoami
oc version
oc get clusterversion
oc get nodes
```

Check cluster operators:

```bash
oc get co
```

Look specifically for:

```text
AVAILABLE
PROGRESSING
DEGRADED
```

A quick view:

```bash
oc get co | awk 'NR==1 || $2=="False" || $3=="True" || $4=="True"'
```

Check cluster version:

```bash
oc get clusterversion version -o yaml
```

If several ClusterOperators are degraded, do not immediately troubleshoot the application itself. First investigate the cluster-level problem.

---

# 4. Step 2 — Check Nodes

Check all nodes:

```bash
oc get nodes -o wide
```

Expected:

```text
STATUS
Ready
```

Investigate:

```bash
oc get nodes
oc describe node <node-name>
```

Check conditions:

```bash
oc get node <node-name> -o jsonpath='{range .status.conditions[*]}{.type}={.status}{" "}{.reason}{"\n"}{end}'
```

Look for:

```text
Ready=False
MemoryPressure=True
DiskPressure=True
PIDPressure=True
NetworkUnavailable=True
```

Check resource consumption:

```bash
oc adm top nodes
```

If metrics are available:

```bash
oc adm top node <node-name>
```

Check pods on the affected node:

```bash
oc get pods -A -o wide --field-selector spec.nodeName=<node-name>
```

Inspect kubelet-related information:

```bash
oc debug node/<node-name>
chroot /host
systemctl status kubelet
journalctl -u kubelet --since "30 min ago"
```

Exit:

```bash
exit
exit
```

---

# 5. Step 3 — Check the Application Namespace

Set the project:

```bash
oc project <namespace>
```

Check everything important:

```bash
oc get all
```

Also check:

```bash
oc get pods -o wide
oc get svc
oc get route
oc get endpoints
oc get endpointslices
oc get pvc
oc get deployment
oc get rs
oc get sts
```

A useful first command:

```bash
oc get pods -o wide
```

Possible states:

```text
Running
Pending
CrashLoopBackOff
ImagePullBackOff
ErrImagePull
CreateContainerConfigError
ContainerCreating
Terminating
Evicted
Completed
```

---

# 6. Step 4 — Troubleshoot Pods

For a failing pod:

```bash
oc describe pod <pod-name>
```

Always check the **Events** section at the bottom.

Get pod status:

```bash
oc get pod <pod-name> -o wide
```

Get detailed YAML:

```bash
oc get pod <pod-name> -o yaml
```

Check container status:

```bash
oc get pod <pod-name> \
  -o jsonpath='{range .status.containerStatuses[*]}{.name}{"  ready="}{.ready}{"  restartCount="}{.restartCount}{"  state="}{.state}{"\n"}{end}'
```

---

# 7. Step 5 — Check Application Logs

Current logs:

```bash
oc logs <pod-name>
```

Specific container:

```bash
oc logs <pod-name> -c <container-name>
```

Previous crashed container:

```bash
oc logs <pod-name> -c <container-name> --previous
```

Follow logs:

```bash
oc logs -f <pod-name>
```

Last 100 lines:

```bash
oc logs <pod-name> --tail=100
```

Logs from the last hour:

```bash
oc logs <pod-name> --since=1h
```

For a deployment:

```bash
oc logs deployment/<deployment-name>
```

Look for:

```text
connection refused
connection timeout
authentication failed
permission denied
OutOfMemoryError
database connection failure
DNS resolution failure
certificate error
TLS handshake failure
file not found
configuration error
```

---

# 8. Step 6 — Check Restart Count

Check restart counts:

```bash
oc get pods
```

Or:

```bash
oc get pods \
  -o custom-columns=NAME:.metadata.name,READY:.status.containerStatuses[*].ready,RESTARTS:.status.containerStatuses[*].restartCount,STATUS:.status.phase
```

High restart count usually indicates:

- Application crash.
- Failed liveness probe.
- Failed readiness/startup probe.
- OOMKill.
- Missing configuration.
- Dependency failure.
- Incorrect command/arguments.

Check termination reason:

```bash
oc get pod <pod-name> \
  -o jsonpath='{range .status.containerStatuses[*]}{.name}{" -> "}{.lastState.terminated.reason}{" exit="}{.lastState.terminated.exitCode}{"\n"}{end}'
```

If you see:

```text
OOMKilled
```

investigate memory.

---

# 9. Step 7 — Check OOM and Resource Problems

Check pod resource configuration:

```bash
oc get pod <pod-name> -o jsonpath='{range .spec.containers[*]}{.name}{" requests="}{.resources.requests}{" limits="}{.resources.limits}{"\n"}{end}'
```

Check node resource usage:

```bash
oc adm top nodes
```

Check pod resource usage:

```bash
oc adm top pods -n <namespace>
```

Look for:

```text
OOMKilled
MemoryPressure
Evicted
```

Check events:

```bash
oc get events --sort-by='.lastTimestamp'
```

Check recent events:

```bash
oc get events --sort-by='.lastTimestamp' | tail -50
```

---

# 10. Step 8 — Check Deployment and ReplicaSet

Check deployment:

```bash
oc get deployment
```

Describe:

```bash
oc describe deployment <deployment-name>
```

Check replicas:

```bash
oc get deployment <deployment-name> \
  -o jsonpath='{.spec.replicas}{" desired / "}{.status.replicas}{" current / "}{.status.availableReplicas}{" available\n"}'
```

Check ReplicaSets:

```bash
oc get rs
```

Describe:

```bash
oc describe rs <replicaset-name>
```

Check rollout status:

```bash
oc rollout status deployment/<deployment-name>
```

Check rollout history:

```bash
oc rollout history deployment/<deployment-name>
```

Check deployment YAML:

```bash
oc get deployment <deployment-name> -o yaml
```

---

# 11. Step 9 — Check Failed Rollout

If a new deployment caused the outage:

```bash
oc rollout history deployment/<deployment-name>
```

Check previous revision:

```bash
oc rollout history deployment/<deployment-name> --revision=<revision-number>
```

If the previous version was healthy:

```bash
oc rollout undo deployment/<deployment-name>
```

Monitor:

```bash
oc rollout status deployment/<deployment-name>
```

Then verify:

```bash
oc get pods
oc get route
```

Do not rollback blindly. First identify whether the new revision actually caused the issue.

---

# 12. Step 10 — Check Service

A pod can be healthy while the application remains unreachable because the Service is incorrect.

Check:

```bash
oc get svc
```

Describe:

```bash
oc describe svc <service-name>
```

Check selector:

```bash
oc get svc <service-name> -o yaml
```

Example:

```yaml
spec:
  selector:
    app: myapp
```

Compare with pod labels:

```bash
oc get pods --show-labels
```

If the Service selector does not match pod labels, the Service may have no endpoints.

---

# 13. Step 11 — Check Endpoints

Check:

```bash
oc get endpoints <service-name>
```

Also check EndpointSlices:

```bash
oc get endpointslices
```

Detailed:

```bash
oc describe endpoints <service-name>
```

If you see:

```text
subsets: <none>
```

or no endpoint addresses, investigate:

- Service selector.
- Pod labels.
- Pod readiness.
- Pod readiness probe.
- EndpointSlice generation.
- NetworkPolicy.

Important concept:

> A pod can be Running but not Ready. Kubernetes/OpenShift Services normally send traffic only to ready endpoints.

---

# 14. Step 12 — Check Readiness Probe

Inspect deployment:

```bash
oc get deployment <deployment-name> -o yaml
```

Look for:

```yaml
readinessProbe:
livenessProbe:
startupProbe:
```

Describe pod:

```bash
oc describe pod <pod-name>
```

Look for:

```text
Readiness probe failed
Liveness probe failed
Startup probe failed
```

Common causes:

```text
Wrong port
Wrong HTTP path
Application starts slowly
Database unavailable
TLS problem
Authentication failure
Incorrect timeout
Incorrect initialDelaySeconds
```

Test from inside the pod when appropriate:

```bash
oc rsh <pod-name>
```

Then:

```bash
curl -v http://127.0.0.1:<port>/<path>
```

Exit:

```bash
exit
```

---

# 15. Step 13 — Check Route

Check:

```bash
oc get route
```

Detailed:

```bash
oc describe route <route-name>
```

Check YAML:

```bash
oc get route <route-name> -o yaml
```

Verify:

```text
Host
Service
TargetPort
TLS
Termination
```

Test route:

```bash
curl -vk https://<route-host>
```

For HTTP:

```bash
curl -v http://<route-host>
```

Check response:

```text
200 = application responding
301/302 = redirect
401/403 = authentication/authorization
404 = route/path/application issue
503 = backend/service/endpoints problem is likely
504 = timeout/backend/network/dependency issue is possible
```

A `503` from the OpenShift router commonly warrants checking the Service and endpoints immediately.

---

# 16. Step 14 — Check Router / Ingress

Check router pods:

```bash
oc get pods -n openshift-ingress -o wide
```

Check deployments:

```bash
oc get deployment -n openshift-ingress
```

Check router logs:

```bash
oc logs -n openshift-ingress deployment/router
```

If multiple router pods exist:

```bash
oc get pods -n openshift-ingress
oc logs -n openshift-ingress <router-pod>
```

Check ingress operator:

```bash
oc get co ingress
oc describe co ingress
```

Check ingress controllers:

```bash
oc get ingresscontroller -n openshift-ingress-operator
```

If the application route is failing while other routes work, focus on the application Service/endpoints first.

If all routes fail, investigate:

```text
Router pods
IngressController
Load balancer
DNS
Firewall
Network
Ingress Operator
```

---

# 17. Step 15 — Test Service Internally

Create a temporary troubleshooting pod if required.

Example:

```bash
oc run network-debug \
  --image=registry.access.redhat.com/ubi9/ubi \
  --restart=Never \
  -it --rm -- bash
```

Inside:

```bash
curl -v http://<service-name>:<port>
```

Test DNS:

```bash
getent hosts <service-name>
```

If DNS utilities are available:

```bash
nslookup <service-name>
```

Test Service IP:

```bash
curl -v http://<cluster-ip>:<port>
```

This helps distinguish:

```text
Route problem
vs
Service problem
vs
Pod/application problem
```

---

# 18. Step 16 — Check OpenShift DNS

Check DNS pods:

```bash
oc get pods -n openshift-dns
```

Check DNS operator:

```bash
oc get co dns
```

Check DNS service:

```bash
oc get svc -n openshift-dns
```

From a troubleshooting pod:

```bash
getent hosts kubernetes.default.svc
```

Test application Service:

```bash
getent hosts <service-name>.<namespace>.svc
```

If service DNS does not resolve, investigate:

```text
CoreDNS/OpenShift DNS
DNS Operator
Network
OVN-Kubernetes
Node connectivity
```

---

# 19. Step 17 — Check NetworkPolicy

List policies:

```bash
oc get networkpolicy
```

Describe:

```bash
oc describe networkpolicy <policy-name>
```

Check whether traffic is allowed between:

```text
Router → Service
Pod → Database
Pod → External service
Namespace A → Namespace B
```

NetworkPolicy problems often appear as:

```text
Connection timeout
Connection refused
Application dependency unavailable
Readiness probe failure
```

Do not delete NetworkPolicies as the first troubleshooting step. Understand the intended traffic flow first.

---

# 20. Step 18 — Check OVN-Kubernetes

Check network operator:

```bash
oc get co network
oc describe co network
```

Check OVN pods:

```bash
oc get pods -n openshift-ovn-kubernetes -o wide
```

Check:

```bash
oc get ds -n openshift-ovn-kubernetes
```

Check logs for affected node:

```bash
oc logs -n openshift-ovn-kubernetes <ovnkube-node-pod>
```

Check network-related events:

```bash
oc get events -A --sort-by='.lastTimestamp' | grep -iE 'network|ovn|dns'
```

If networking is broken across many namespaces, treat it as a cluster-level incident.

---

# 21. Step 19 — Check PVC and Storage

If the application uses persistent storage:

```bash
oc get pvc
```

Check:

```bash
oc describe pvc <pvc-name>
```

Check PV:

```bash
oc get pv
```

Check StorageClasses:

```bash
oc get storageclass
```

Check pod events:

```bash
oc describe pod <pod-name>
```

Look for:

```text
FailedMount
FailedAttachVolume
FailedMountVolume
MountVolume.SetUp failed
Multi-Attach error
PVC Pending
```

Check CSI drivers:

```bash
oc get csidrivers
```

Check storage operators:

```bash
oc get co | grep -i storage
```

For OpenShift Data Foundation or another storage platform, also inspect the relevant storage namespace and operator status.

---

# 22. Step 20 — Check PVC Status

Expected:

```text
STATUS
Bound
```

If:

```text
Pending
```

investigate:

```bash
oc describe pvc <pvc-name>
```

Check:

```bash
oc get pv
oc get sc
```

Possible causes:

```text
No available PV
Dynamic provisioning failure
CSI driver failure
Storage backend unavailable
Wrong StorageClass
Access mode mismatch
Insufficient capacity
Topology constraint
```

---

# 23. Step 21 — Check ConfigMaps and Secrets

Applications frequently fail because configuration changed.

Check:

```bash
oc get configmap
oc get secret
```

Inspect ConfigMap:

```bash
oc get configmap <configmap-name> -o yaml
```

Inspect Secret metadata:

```bash
oc describe secret <secret-name>
```

Do not expose secret values unnecessarily.

Check pod references:

```bash
oc get deployment <deployment-name> -o yaml
```

Look for:

```yaml
env:
envFrom:
configMapKeyRef:
secretKeyRef:
volumes:
secret:
configMap:
```

Common errors:

```text
Missing environment variable
Wrong database hostname
Wrong credentials
Expired certificate
Missing mounted file
Wrong configuration key
```

---

# 24. Step 22 — Check Image Pull Problems

If pod status is:

```text
ImagePullBackOff
ErrImagePull
```

Run:

```bash
oc describe pod <pod-name>
```

Check image:

```bash
oc get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```

Investigate:

```text
Image does not exist
Registry unavailable
Authentication failure
ImagePullSecret missing
Network connectivity
Certificate trust problem
Architecture mismatch
Registry rate limit
```

Check image pull secrets:

```bash
oc get secrets
```

Check ServiceAccount:

```bash
oc get sa <service-account> -o yaml
```

---

# 25. Step 23 — Check SecurityContext / SCC

If a pod fails to start because of permissions or security:

```bash
oc describe pod <pod-name>
```

Check ServiceAccount:

```bash
oc get pod <pod-name> -o jsonpath='{.spec.serviceAccountName}{"\n"}'
```

Check SCC:

```bash
oc get scc
```

Check which SCC may be applicable:

```bash
oc adm policy who-can use scc/<scc-name>
```

Typical errors:

```text
permission denied
operation not permitted
runAsUser not allowed
privileged container denied
volume permission failure
```

Do not make an application privileged just to make it work. Identify the exact security requirement.

---

# 26. Step 24 — Check Events

Events are one of the fastest ways to identify scheduling, storage, image, and probe failures.

Namespace:

```bash
oc get events --sort-by='.lastTimestamp'
```

All namespaces:

```bash
oc get events -A --sort-by='.lastTimestamp'
```

Useful filtering:

```bash
oc get events -A --sort-by='.lastTimestamp' | grep -iE 'failed|warning|error|backoff|unhealthy'
```

Look for:

```text
FailedScheduling
FailedMount
FailedAttachVolume
FailedCreatePodSandBox
BackOff
Unhealthy
Failed
Evicted
OOMKilled
```

---

# 27. Step 25 — Check Scheduling

If a pod is:

```text
Pending
```

Run:

```bash
oc describe pod <pod-name>
```

Look at Events.

Common causes:

```text
Insufficient CPU
Insufficient memory
NodeSelector mismatch
Affinity/anti-affinity
Taints/tolerations
PVC topology
PodSecurity restrictions
ResourceQuota
LimitRange
```

Check resource quota:

```bash
oc get resourcequota
oc describe resourcequota <quota-name>
```

Check LimitRange:

```bash
oc get limitrange
oc describe limitrange <limitrange-name>
```

Check node labels:

```bash
oc get nodes --show-labels
```

Check taints:

```bash
oc describe node <node-name> | grep -i taint
```

---

# 28. Step 26 — Check ResourceQuota

If a deployment cannot create pods:

```bash
oc get resourcequota
```

Describe:

```bash
oc describe resourcequota <quota-name>
```

Look for exhausted:

```text
requests.cpu
requests.memory
limits.cpu
limits.memory
pods
persistentvolumeclaims
```

---

# 29. Step 27 — Check Application Dependencies

An application may be healthy but unable to connect to:

```text
Database
Redis
Kafka
RabbitMQ
LDAP
External API
Storage
DNS
Another microservice
```

Test from the application pod or debugging pod:

```bash
curl -v http://<dependency>:<port>
```

For TCP connectivity:

```bash
nc -vz <host> <port>
```

For DNS:

```bash
getent hosts <hostname>
```

For TLS:

```bash
openssl s_client -connect <host>:443 -servername <host>
```

Check application logs for:

```text
connection timeout
connection refused
TLS handshake failure
certificate verify failed
authentication failed
unknown host
```

---

# 30. Step 28 — Check Certificates and TLS

For HTTPS applications:

```bash
curl -vk https://<host>
```

Check route:

```bash
oc describe route <route-name>
```

Check secrets used by the application:

```bash
oc get secret
```

Check certificate expiry where appropriate:

```bash
openssl s_client -connect <host>:443 -servername <host> </dev/null 2>/dev/null | openssl x509 -noout -dates
```

Possible problems:

```text
Expired certificate
Wrong SAN
Wrong hostname
Missing CA
Incorrect TLS termination
Backend HTTPS/HTTP mismatch
```

For Route TLS, understand the termination type:

```text
edge
reencrypt
passthrough
```

---

# 31. Step 29 — Check Service Target Port

A very common issue is:

```text
Service port != container port
```

Check:

```bash
oc get svc <service-name> -o yaml
```

Check container ports:

```bash
oc get deployment <deployment-name> -o yaml
```

Verify:

```text
Service.spec.ports.targetPort
```

matches the application container port or named port.

Test directly:

```bash
oc rsh <pod-name>
curl -v http://127.0.0.1:<container-port>
```

If localhost works but Service access fails, investigate Service configuration and endpoints.

---

# 32. Step 30 — Check Application Process Inside Container

Enter container:

```bash
oc rsh <pod-name>
```

Check processes:

```bash
ps -ef
```

Check listening ports:

```bash
ss -lntp
```

Test local application:

```bash
curl -v http://127.0.0.1:<port>
```

Check filesystem:

```bash
df -h
```

Check memory:

```bash
free -m
```

Check environment:

```bash
env
```

Be careful not to expose secrets when displaying environment variables.

Exit:

```bash
exit
```

---

# 33. Step 31 — Check Node Disk Space

If pods are being evicted or images cannot be pulled:

```bash
oc describe node <node-name>
```

Look for:

```text
DiskPressure=True
```

Debug node:

```bash
oc debug node/<node-name>
chroot /host
df -h
df -ih
```

Check container storage:

```bash
du -sh /var/lib/containers/*
```

Check inode usage:

```bash
df -ih
```

Exit:

```bash
exit
exit
```

---

# 34. Step 32 — Check Control Plane Health

If many applications fail simultaneously:

```bash
oc get co
```

Check:

```bash
oc get nodes
oc get clusterversion
```

Check API availability:

```bash
oc whoami
oc get --raw='/readyz?verbose'
```

Check API server pods:

```bash
oc get pods -n openshift-kube-apiserver
```

Check etcd:

```bash
oc get pods -n openshift-etcd
```

Check controller manager:

```bash
oc get pods -n openshift-kube-controller-manager
```

Check scheduler:

```bash
oc get pods -n openshift-kube-scheduler
```

If the API is unstable, investigate control-plane health before application-level changes.

---

# 35. Step 33 — Check etcd

Check:

```bash
oc get pods -n openshift-etcd
```

Check operator:

```bash
oc get co etcd
```

Describe:

```bash
oc describe co etcd
```

Look for:

```text
degraded
high latency
member unavailable
leader election problems
disk problems
certificate problems
```

Do not manually modify etcd data.

---

# 36. Step 34 — Check Cluster Operators

Run:

```bash
oc get co
```

Important operators include:

```text
authentication
console
dns
etcd
ingress
kube-apiserver
kube-controller-manager
kube-scheduler
machine-api
network
storage
image-registry
```

For a degraded operator:

```bash
oc describe co <operator-name>
```

Check related pods:

```bash
oc get pods -n <operator-namespace>
```

Check logs:

```bash
oc logs -n <operator-namespace> <pod-name>
```

---

# 37. Step 35 — Check Recent Cluster Changes

Ask:

```text
What changed before the outage?
```

Check:

```bash
oc get events -A --sort-by='.lastTimestamp'
```

Check deployments:

```bash
oc get deployment -A
```

Check recent rollout history:

```bash
oc rollout history deployment/<deployment-name>
```

Potential changes:

```text
Application deployment
Image update
ConfigMap update
Secret update
Route change
NetworkPolicy
SCC
Node maintenance
Certificate renewal
Storage change
Operator upgrade
OpenShift upgrade
MachineConfig change
DNS change
Firewall change
Load balancer change
```

The timeline often identifies the root cause faster than random testing.

---

# 38. Step 36 — Check MachineConfig / MCO if Node-Level Changes Were Made

Check:

```bash
oc get mcp
```

Detailed:

```bash
oc describe mcp master
oc describe mcp worker
```

Check MachineConfig:

```bash
oc get mc
```

Check MCO:

```bash
oc get co machine-config
```

If nodes are updating or degraded:

```bash
oc get mcp
oc get nodes
```

A MachineConfig change can affect:

```text
Networking
Container runtime
Kubelet
Certificates
OS configuration
Kernel settings
Firewall
System services
```

---

# 39. Step 37 — Check Image Registry

If new applications fail to start because images cannot be pulled:

```bash
oc get co image-registry
```

Check registry pods:

```bash
oc get pods -n openshift-image-registry
```

Check:

```bash
oc get configs.imageregistry cluster -o yaml
```

Check image streams:

```bash
oc get is -A
```

If required:

```bash
oc describe pod <pod-name>
```

Look for registry authentication or connectivity errors.

---

# 40. Step 38 — Check Authentication Only When Relevant

If the application itself is returning:

```text
401
403
```

determine whether the failure is:

```text
Application authentication
OpenShift OAuth
LDAP/Identity Provider
ServiceAccount
RBAC
Route authentication
```

Check OAuth configuration:

```bash
oc get oauth cluster -o yaml
```

Check authentication operator:

```bash
oc get co authentication
```

For OpenShift user access:

```bash
oc whoami
oc auth can-i get pods -n <namespace>
```

For ServiceAccount authorization:

```bash
oc auth can-i get pods \
  --as=system:serviceaccount:<namespace>:<serviceaccount>
```

---

# 41. Step 39 — Check RBAC

List role bindings:

```bash
oc get rolebinding -n <namespace>
```

Cluster role bindings:

```bash
oc get clusterrolebinding
```

Test:

```bash
oc auth can-i <verb> <resource> -n <namespace>
```

Example:

```bash
oc auth can-i get pods -n myapp
```

For a ServiceAccount:

```bash
oc auth can-i get secrets \
  --as=system:serviceaccount:myapp:my-sa \
  -n myapp
```

---

# 42. Step 40 — Check Network From Node

If pod networking is suspected:

```bash
oc debug node/<node-name>
chroot /host
```

Check:

```bash
ip addr
ip route
ss -lntp
```

Check DNS configuration:

```bash
cat /etc/resolv.conf
```

Check network services as appropriate.

Exit:

```bash
exit
exit
```

---

# 43. Step 41 — Compare Healthy and Failed Applications

If one application works and another does not, compare:

```bash
oc get route <route> -o yaml
oc get svc <service> -o yaml
oc get deployment <deployment> -o yaml
oc get pods -o wide
```

Compare:

```text
Labels
Selectors
Ports
ServiceAccount
SCC
NetworkPolicy
Resources
Probes
ConfigMaps
Secrets
PVCs
Routes
Images
Node placement
```

This is often faster than troubleshooting the entire cluster.

---

# 44. Step 42 — Verify Recovery

After making a change:

```bash
oc get pods
```

Then:

```bash
oc get endpoints
```

Then:

```bash
oc get route
```

Then:

```bash
curl -vk https://<route-host>
```

Check application logs:

```bash
oc logs <pod-name> --tail=100
```

Check rollout:

```bash
oc rollout status deployment/<deployment-name>
```

Confirm:

```text
Pods Ready
Service has endpoints
Route works
Application responds
No repeated restarts
No new warning events
No ClusterOperator degradation
```

---

# 45. Important Diagnostic Matrix

| Symptom | First Commands | Likely Area |
|---|---|---|
| Route returns 503 | `oc get route`, `oc get svc`, `oc get endpoints` | Service/Pod |
| Route returns 404 | `oc describe route` | Route/path |
| Route returns 504 | Router logs + endpoints + network | Backend/network |
| Pod CrashLoopBackOff | `oc logs --previous`, `oc describe pod` | Application |
| Pod Pending | `oc describe pod` | Scheduler/resources |
| ImagePullBackOff | `oc describe pod` | Registry/image/auth |
| ContainerCreating | `oc describe pod` | Volume/network/image |
| OOMKilled | `oc adm top pods`, pod resources | Memory |
| Pod Running but app unavailable | Service/endpoints/probes | App/service |
| Service has no endpoints | `oc get endpoints`, labels | Selector/readiness |
| PVC Pending | `oc describe pvc` | Storage |
| FailedMount | `oc describe pod` | Storage/CSI |
| All routes down | `oc get co`, ingress/router | Ingress/network |
| All apps down | `oc get co`, nodes, API | Cluster |
| DNS failure | `oc get co dns`, DNS pods | DNS/network |
| Connection timeout | NetworkPolicy/OVN/DNS | Network |
| 401/403 | `oc auth can-i`, app logs | Auth/RBAC |
| Node NotReady | `oc describe node`, kubelet | Node |
| DiskPressure | `oc describe node`, `df -h` | Disk |
| MemoryPressure | `oc adm top nodes` | Memory |

---

# 46. Recommended L3 Troubleshooting Decision Tree

## Case A — User reports HTTP 503

Run:

```bash
oc get route -n <namespace>
oc get svc -n <namespace>
oc get endpoints -n <namespace>
oc get pods -n <namespace> -o wide
```

If endpoints are empty:

```bash
oc describe svc <service>
oc get pods --show-labels
oc describe pod <pod>
```

If endpoints exist:

```bash
oc logs <pod>
oc describe pod <pod>
```

Then test internally:

```bash
curl -v http://<service>:<port>
```

Then externally:

```bash
curl -vk https://<route>
```

---

# 47. Case B — Pod is CrashLoopBackOff

Run:

```bash
oc describe pod <pod>
oc logs <pod> --previous
oc get pod <pod> -o yaml
```

Check:

```text
Exit code
Termination reason
OOMKilled
Probe failures
ConfigMap
Secret
Environment
Volume mounts
Application dependencies
```

If OOM:

```bash
oc adm top pod <pod>
oc adm top nodes
```

If probe failure:

```bash
oc describe pod <pod>
```

If configuration failure:

```bash
oc get deployment <deployment> -o yaml
```

---

# 48. Case C — Pod is Pending

Run:

```bash
oc describe pod <pod>
```

Read Events.

Then:

```bash
oc get nodes
oc describe node <node>
oc get resourcequota
oc get limitrange
```

Investigate:

```text
CPU
Memory
Taints
Tolerations
Affinity
NodeSelector
PVC
Quota
```

---

# 49. Case D — Application Works Inside Pod but Not Through Route

Test:

```bash
oc rsh <pod>
curl -v http://127.0.0.1:<port>
```

If successful:

```bash
oc get svc
oc get endpoints
oc describe svc <service>
oc get route
oc describe route <route>
```

Then:

```bash
curl -vk https://<route-host>
```

Likely areas:

```text
Service
TargetPort
Endpoint
Router
Route TLS
Network
DNS
Load balancer
```

---

# 50. Case E — All Applications Are Down

Do not troubleshoot every application individually.

Start here:

```bash
oc get nodes
oc get co
oc get clusterversion
oc get --raw='/readyz?verbose'
```

Then:

```bash
oc get pods -n openshift-ingress
oc get pods -n openshift-dns
oc get pods -n openshift-ovn-kubernetes
```

Then:

```bash
oc get pods -n openshift-kube-apiserver
oc get pods -n openshift-etcd
```

Likely root-cause domains:

```text
Network
DNS
Ingress
API/control plane
Storage
Node failure
Certificate failure
OpenShift upgrade
MachineConfig
Infrastructure/load balancer
```

---

# 51. Golden Commands for an Initial Incident

Run these first:

```bash
oc whoami
oc version
oc get nodes -o wide
oc get co
oc get clusterversion
oc get events -A --sort-by='.lastTimestamp' | tail -100
```

Then application namespace:

```bash
oc project <namespace>

oc get pods -o wide
oc get svc
oc get route
oc get endpoints
oc get endpointslices
oc get pvc
oc get deployment
```

Then:

```bash
oc describe pod <pod-name>
oc logs <pod-name> --tail=200
oc logs <pod-name> --previous --tail=200
```

Then:

```bash
oc describe svc <service-name>
oc describe route <route-name>
```

---

# 52. Evidence Collection Before Making Changes

Before restarting or deleting anything, collect:

```bash
oc get pods -o wide
oc get svc
oc get route
oc get endpoints
oc get events --sort-by='.lastTimestamp'
oc describe pod <pod>
oc logs <pod> --tail=500
oc get deployment <deployment> -o yaml
oc get svc <service> -o yaml
oc get route <route> -o yaml
```

For cluster-level problems:

```bash
oc get nodes -o wide
oc get co
oc get clusterversion
oc get mcp
oc get events -A --sort-by='.lastTimestamp'
```

Preserve the evidence before changing the system.

---

# 53. Common Mistakes to Avoid

## Do not immediately delete the pod

Bad first action:

```bash
oc delete pod <pod>
```

This may hide the original failure.

First:

```bash
oc describe pod <pod>
oc logs <pod> --previous
```

---

## Do not immediately restart the deployment

Bad:

```bash
oc rollout restart deployment/<deployment>
```

First determine why it failed.

---

## Do not delete NetworkPolicies blindly

Bad:

```bash
oc delete networkpolicy --all
```

This can create a security incident.

---

## Do not modify SCC blindly

Do not solve application problems by assigning:

```text
privileged
```

without understanding the security requirement.

---

## Do not change MachineConfig during an application incident unless required

MachineConfig changes can reboot/update nodes and expand the outage.

---

# 54. Production Incident Workflow

Use this sequence during a real incident:

```text
1. Confirm impact
        ↓
2. Determine scope
        ↓
3. Check ClusterOperators
        ↓
4. Check nodes
        ↓
5. Check application pods
        ↓
6. Check application logs
        ↓
7. Check deployment/replicas
        ↓
8. Check Service
        ↓
9. Check endpoints
        ↓
10. Check Route
        ↓
11. Check DNS/network
        ↓
12. Check storage
        ↓
13. Check dependencies
        ↓
14. Identify recent change
        ↓
15. Fix/rollback
        ↓
16. Verify
        ↓
17. Monitor
        ↓
18. Document RCA
```

---

# 55. Example L3 Incident

## Problem

Users report:

```text
https://app.example.com
returns HTTP 503
```

## Step 1 — Route

```bash
oc get route app
```

Route exists.

## Step 2 — Service

```bash
oc get svc app
```

Service exists.

## Step 3 — Endpoints

```bash
oc get endpoints app
```

Output:

```text
ENDPOINTS: <none>
```

This immediately narrows the investigation.

## Step 4 — Pods

```bash
oc get pods --show-labels
```

Pods are:

```text
Running
```

but:

```text
READY 0/1
```

## Step 5 — Describe Pod

```bash
oc describe pod <pod>
```

Events show:

```text
Readiness probe failed
```

## Step 6 — Logs

```bash
oc logs <pod>
```

Application reports:

```text
Database connection refused
```

## Step 7 — Dependency

Test:

```bash
oc rsh <pod>
curl -v http://database:5432
```

Connection fails.

## Root Cause

The application itself was running, but the database dependency was unavailable. Therefore:

```text
Pod Running
        ↓
Readiness probe failed
        ↓
Pod not Ready
        ↓
No Service endpoint
        ↓
Router has no backend
        ↓
HTTP 503
```

This is an important L3 troubleshooting pattern.

---

# 56. Another Example — ImagePullBackOff

Problem:

```text
Pod = ImagePullBackOff
```

Run:

```bash
oc describe pod <pod>
```

Event:

```text
Failed to pull image
authentication required
```

Check:

```bash
oc get sa <service-account> -o yaml
oc get secret
```

Verify image pull secret is correctly associated with the ServiceAccount.

Do not modify the node or restart the cluster. The problem is most likely image registry authentication.

---

# 57. Another Example — Pending Pod

Problem:

```text
Pod = Pending
```

Run:

```bash
oc describe pod <pod>
```

Event:

```text
0/3 nodes are available:
Insufficient memory
```

Check:

```bash
oc adm top nodes
```

Possible remediation:

```text
Reduce application resource requests
Scale workload appropriately
Add worker capacity
Move workloads
Free resources
```

Do not simply delete the pod. It will remain Pending because the scheduler still cannot place it.

---

# 58. Another Example — NetworkPolicy

Problem:

```text
Application returns database timeout
```

Pod is Ready.

Test:

```bash
oc rsh <pod>
nc -vz database 5432
```

Timeout.

Check:

```bash
oc get networkpolicy
oc describe networkpolicy <policy>
```

If a recent NetworkPolicy change blocks traffic from the application namespace to the database namespace, correct the policy according to the intended security design.

---

# 59. Final L3 Mental Model

When troubleshooting an OpenShift application outage, think in layers:

```text
Layer 1 — User
    |
    | Can user resolve the hostname?
    v
Layer 2 — DNS
    |
    | Does DNS resolve?
    v
Layer 3 — Load Balancer / Router
    |
    | Does Route reach the router?
    v
Layer 4 — Route
    |
    | Does Route point to correct Service?
    v
Layer 5 — Service
    |
    | Does Service have endpoints?
    v
Layer 6 — EndpointSlice
    |
    | Are pods Ready?
    v
Layer 7 — Pod
    |
    | Is container running?
    v
Layer 8 — Application
    |
    | Is process listening?
    v
Layer 9 — Dependencies
    |
    | DB / API / Redis / LDAP / Kafka
    v
Layer 10 — Network
    |
    | DNS / NetworkPolicy / OVN
    v
Layer 11 — Storage
    |
    | PVC / CSI / backend
    v
Layer 12 — Node
    |
    | CPU / memory / disk / kubelet
    v
Layer 13 — Cluster
    |
    | Operators / API / etcd
    v
Layer 14 — Infrastructure
    |
    | DNS / LB / firewall / virtualization / physical network
```

---

# 60. Interview-Ready Answer

If asked:

> **"How do you troubleshoot an OpenShift cluster where applications are down?"**

A strong L3 answer is:

> "I first determine the scope and impact—whether one application, one namespace, or the entire cluster is affected. I verify API access, node health, ClusterOperators and cluster version. For the affected application, I check pods, deployment/ReplicaSet status, events, restart counts, logs and probes. Then I follow the traffic path from Route to Service to EndpointSlice to Ready pods. If the route returns 503, I immediately verify whether the Service has endpoints and whether the selected pods are Ready. I then validate application ports, DNS, NetworkPolicy, OVN networking, storage/PVCs and external dependencies such as databases. For Pending pods I investigate scheduler events, resource requests, taints, affinity and quotas. For CrashLoopBackOff I inspect current and previous container logs, exit codes, probes, configuration and OOMKills. If multiple applications are affected, I shift to cluster-level troubleshooting—Ingress, DNS, network, storage, nodes, API server, etcd and ClusterOperators. I always collect evidence before restarting or deleting resources, identify the recent change that triggered the incident, remediate or rollback safely, and finally verify application availability and cluster health." 

---

# 61. Quick Production Checklist

- [ ] Confirm user impact.
- [ ] Determine whether one application or the entire cluster is affected.
- [ ] Verify `oc whoami`.
- [ ] Check `oc get nodes`.
- [ ] Check `oc get co`.
- [ ] Check `oc get clusterversion`.
- [ ] Check recent events.
- [ ] Check application pods.
- [ ] Check pod restart count.
- [ ] Check `CrashLoopBackOff`, `Pending`, `ImagePullBackOff`, `OOMKilled`.
- [ ] Check pod logs.
- [ ] Check previous container logs.
- [ ] Check Deployment/ReplicaSet.
- [ ] Check rollout status/history.
- [ ] Check Service.
- [ ] Check Service selector.
- [ ] Check Endpoints.
- [ ] Check EndpointSlices.
- [ ] Check readiness/liveness/startup probes.
- [ ] Check Route.
- [ ] Test Route with `curl`.
- [ ] Check router/Ingress.
- [ ] Check DNS.
- [ ] Check NetworkPolicy.
- [ ] Check OVN-Kubernetes.
- [ ] Check PVC/PV/CSI.
- [ ] Check ConfigMaps/Secrets.
- [ ] Check image registry/image pull secrets.
- [ ] Check SCC/RBAC if permissions are involved.
- [ ] Check CPU/memory/disk pressure.
- [ ] Check ResourceQuota/LimitRange.
- [ ] Check application dependencies.
- [ ] Check recent changes.
- [ ] Collect evidence before making disruptive changes.
- [ ] Remediate or rollback.
- [ ] Verify Route → Service → Endpoint → Pod → Application.
- [ ] Monitor for recurrence.
- [ ] Document root cause and corrective action.

---

# 62. Most Important Commands to Memorize

```bash
oc get nodes -o wide

oc get co

oc get clusterversion

oc get events -A --sort-by='.lastTimestamp'

oc get pods -A -o wide

oc describe pod <pod>

oc logs <pod>

oc logs <pod> --previous

oc get deployment

oc rollout status deployment/<deployment>

oc rollout history deployment/<deployment>

oc get svc

oc describe svc <service>

oc get endpoints

oc get endpointslices

oc get route

oc describe route <route>

oc get pvc

oc describe pvc <pvc>

oc adm top nodes

oc adm top pods -A

oc get networkpolicy

oc get mcp

oc get --raw='/readyz?verbose'
```

**Golden rule:**

> **Do not ask "Which command restarts the application?" Ask "At which layer is the failure occurring?"**

Once the failing layer is identified, the remediation becomes much safer and faster.
