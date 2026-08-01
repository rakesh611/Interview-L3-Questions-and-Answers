# Corporate-Level Kubernetes Administrator L3 Interview Questions and Detailed Answers

**Target audience:** Senior Kubernetes Administrators, Platform Engineers, SREs, DevOps Engineers, and candidates with approximately 8–12+ years of infrastructure experience.

**Technical baseline:** Kubernetes v1.36-era administration. Adapt every command to the installed distribution, container runtime, CNI, CSI, operating system, and cloud provider.

> **L3 interview expectation:** Do not stop at definitions. Explain the control flow, failure domains, validation commands, output interpretation, production risk, safe remediation, and rollback approach.

---

## Table of Contents

1. Cluster Architecture and Control Plane
2. Nodes, Kubelet, Runtime, and Pod Lifecycle
3. Workload Controllers and Application Operations
4. Services, DNS, CNI, Ingress, and Gateway API
5. Scheduling, Resources, Autoscaling, and Eviction
6. Persistent Storage, CSI, Backup, and Recovery
7. Authentication, RBAC, Admission, and Security
8. Upgrades, Certificates, etcd, and Disaster Recovery
9. Monitoring, Logging, Auditing, and Capacity
10. Production Troubleshooting Scenarios
11. Enterprise Governance and L3 Design
12. Rapid-Fire Command Questions
13. Official References

---

# 1. Cluster Architecture and Control Plane

## 1. Explain Kubernetes architecture at an L3 administrator level.

A Kubernetes cluster consists of a **control plane** and one or more **worker nodes**. The control plane exposes the Kubernetes API, stores cluster state, schedules Pods, and runs controllers that reconcile actual state with desired state. Worker nodes run Pods through kubelet and a CRI-compatible container runtime.

Core control-plane components:

- **kube-apiserver:** Authenticates, authorizes, admits, validates, and persists API objects.
- **etcd:** Strongly consistent backing store for Kubernetes API data.
- **kube-scheduler:** Selects an eligible node for each unscheduled Pod.
- **kube-controller-manager:** Runs controllers such as Deployment, ReplicaSet, Node, Job, EndpointSlice, namespace, and service-account controllers.
- **cloud-controller-manager:** Integrates with cloud APIs when applicable.

Worker-node components:

- **kubelet:** Reconciles PodSpecs assigned to its node and reports status.
- **container runtime:** Runs containers through CRI, commonly containerd or CRI-O.
- **CNI implementation:** Configures Pod networking and often NetworkPolicy.
- **Service data plane:** kube-proxy or an eBPF-based replacement implements Service forwarding.

Kubernetes is an **API-driven reconciliation system**, not a collection of scripts. Users and controllers write desired state to the API; controllers watch objects and act until observed state converges.

Useful checks:

```bash
kubectl cluster-info
kubectl get --raw='/readyz?verbose'
kubectl get nodes -o wide
kubectl get pods -n kube-system -o wide
kubectl api-resources
```

Do not use legacy `kubectl get componentstatuses` as the main health check. Prefer health endpoints, component metrics, static-Pod status, logs, and distribution-specific diagnostics.

## 2. Describe the complete flow when a user runs `kubectl apply -f deployment.yaml`.

1. `kubectl` loads kubeconfig, context, API endpoint, and credentials.
2. It sends an HTTPS request to kube-apiserver.
3. **Authentication** identifies the caller.
4. **Authorization** decides whether the caller can perform the verb on the resource.
5. **Mutating admission** can modify the request.
6. Schema and built-in validation run.
7. **Validating admission** can allow or reject the object.
8. The API server stores the accepted object in etcd.
9. The Deployment controller observes it and creates or updates a ReplicaSet.
10. The ReplicaSet controller creates Pods.
11. The scheduler filters and scores nodes, then binds each Pod.
12. Kubelet on the selected node asks the runtime to create the Pod sandbox, invokes CNI, mounts CSI volumes, pulls images, and starts containers.
13. Kubelet and controllers update status.
14. EndpointSlice and Service data-plane state are updated for ready backends.

Validation commands:

```bash
kubectl auth can-i create deployments -n app
kubectl get deployment,rs,pod -n app -w
kubectl describe pod <pod> -n app
kubectl get events -n app --sort-by=.metadata.creationTimestamp
kubectl get endpointslices -n app
kubectl rollout status deployment/<name> -n app
```

An accepted API request does not prove the application is running. Later stages can fail because of scheduling, image pull, CNI, CSI, security policy, probes, or application errors.

## 3. What is the Kubernetes reconciliation loop?

A reconciliation loop repeatedly compares **desired state** with **observed state** and performs actions to reduce the difference. Controllers are generally level-driven, so they can converge even when an individual event is missed.

Example: a Deployment requests five replicas. If four ready Pods exist, the responsible controllers create another Pod. If a managed Pod fails, the controller replaces it.

Important implications:

- Kubernetes is eventually consistent across controllers.
- Status can lag behind specification changes.
- Manual edits to controller-owned objects may be overwritten.
- Troubleshooting should identify the responsible controller and inspect its inputs, conditions, events, logs, and owner references.
- Declarative changes are safer because they can be reviewed and versioned.

```bash
kubectl get deployment web -o yaml
kubectl get rs -l app=web -o yaml
kubectl get pod <pod> -o jsonpath='{.metadata.ownerReferences}'
```

`metadata.generation` increases when desired state changes. Controllers may report the processed version in `status.observedGeneration`. If observed generation is behind, reconciliation has not completed or the controller is unhealthy.

## 4. Why is etcd critical, and how would you design it for production?

etcd stores the authoritative state exposed by the Kubernetes API: workloads, RBAC, Secrets unless externalized, leases, custom resources, and other control-plane data.

Production principles:

- Use an odd number of voting members, normally three or five.
- Maintain quorum and avoid simultaneous member maintenance.
- Place members across failure domains while keeping latency low.
- Use reliable, fast storage with good fsync latency.
- Prevent CPU, memory, disk, and network starvation.
- Protect client and peer traffic with TLS and firewall controls.
- Monitor leader changes, proposal latency, fsync latency, database size, quota, alarms, and member health.
- Take encrypted off-host snapshots and test restore.
- Retain API-server encryption configuration and KMS keys.

Common kubeadm checks:

```bash
kubectl -n kube-system get pods -l component=etcd -o wide
sudo crictl ps --name etcd
sudo grep -nE 'listen|advertise|data-dir' /etc/kubernetes/manifests/etcd.yaml
```

Health example, with paths adapted to the environment:

```bash
export ETCDCTL_API=3
sudo etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key \
  endpoint health --cluster
```

A snapshot is not proven backup until its metadata, checksum, off-host copy, version compatibility, and restoration are validated.

## 5. Explain etcd quorum and what happens when quorum is lost.

Quorum is the majority of voting members required to commit changes:

```text
quorum = floor(number_of_voting_members / 2) + 1
```

Examples:

- 1 member: tolerates 0 failures.
- 3 members: quorum 2, tolerates 1 failure.
- 5 members: quorum 3, tolerates 2 failures.

When quorum is lost, existing containers often continue running, but the control plane cannot reliably commit new state. API writes fail or hang, controllers cannot persist changes, new Pods cannot be scheduled, and endpoint or leader-election updates may fail.

Safe response:

1. Stop random membership changes.
2. Establish which members and data directories are healthy.
3. Check network, disk, time, certificates, and process status.
4. Recover failed members while quorum still exists.
5. If quorum cannot be recovered, follow a tested snapshot-restore procedure.

```bash
etcdctl endpoint status --cluster -w table
etcdctl endpoint health --cluster
etcdctl member list -w table
```

A five-member cluster is not automatically better; it adds network and consensus overhead. Use it only when the extra failure tolerance is justified.

## 6. How does a highly available control plane work?

Multiple API servers normally run behind a stable VIP or load balancer. API servers are active and mostly stateless; all use the same etcd cluster and consistent security/admission configuration.

Multiple scheduler and controller-manager instances run, but Lease-based leader election ensures one active leader for each responsibility while others wait to take over.

A robust design includes:

- Three or more control-plane nodes.
- Three or five etcd members.
- A redundant API load balancer.
- Health checks against `/readyz`, not only TCP 6443.
- Failure-domain separation.
- Consistent static-Pod manifests and certificates.
- Tested upgrades and backups.
- HA for identity, DNS, CNI, CSI, admission webhooks, and ingress components.

```bash
kubectl get lease -n kube-system
kubectl get pods -n kube-system -o wide
kubectl get --raw='/readyz?verbose'
```

API-server redundancy cannot compensate for etcd quorum loss.

## 7. What are static Pods, and how does kubeadm use them?

A static Pod is managed directly by kubelet from a local manifest rather than by a workload controller. kubeadm commonly places control-plane manifests in:

```text
/etc/kubernetes/manifests/
```

Typical files define kube-apiserver, kube-controller-manager, kube-scheduler, and local etcd. Kubelet watches the directory and recreates containers when a manifest changes. Mirror Pods appear in the API for visibility, but deleting a mirror Pod does not remove the static Pod permanently.

```bash
sudo ls -l /etc/kubernetes/manifests
sudo crictl ps --name kube-apiserver
kubectl -n kube-system get pod -l component=kube-apiserver -o wide
sudo journalctl -u kubelet -n 200 --no-pager
```

Safe practice:

- Back up manifests before editing.
- Change one control-plane node at a time.
- Do not leave backup YAML files in the manifest directory.
- Retain node-level access and `crictl` because a bad API-server manifest can make `kubectl` unavailable.

## 8. How are Leases used in Kubernetes?

Leases in `coordination.k8s.io` support:

- Leader election for scheduler and controller-manager instances.
- Lightweight node heartbeats in the `kube-node-lease` namespace.

```bash
kubectl get lease -n kube-system
kubectl describe lease kube-scheduler -n kube-system
kubectl get lease -n kube-node-lease
kubectl get lease <node> -n kube-node-lease -o yaml
```

If leader-election renewal repeatedly fails, inspect API latency, etcd write latency, control-plane network loss, CPU starvation, clock synchronization, and authorization to update Leases. Do not reduce timeouts before proving the underlying platform is healthy.

# 2. Nodes, Kubelet, Runtime, and Pod Lifecycle

## 9. What does kubelet do, and what does it not do?

Kubelet is the node agent. It watches PodSpecs assigned to its node, ensures containers and volumes match those specifications, runs probes, reports Pod/node status, and coordinates with CRI, CNI, and CSI components.

Kubelet does **not** schedule Pods. The scheduler binds Pods to nodes; controllers create Pods; kubelet executes the node-level desired state.

Common locations in kubeadm systems:

```text
/var/lib/kubelet/
/var/lib/kubelet/config.yaml
/etc/kubernetes/kubelet.conf
/etc/systemd/system/kubelet.service.d/
```

```bash
systemctl status kubelet
journalctl -u kubelet --since '30 min ago'
ps -ef | grep [k]ubelet
kubectl describe node <node>
```

Typical failures include expired client certificates, API connectivity problems, runtime socket failure, CNI initialization failure, disk/inode pressure, invalid configuration, cgroup-driver mismatch, and bootstrap or certificate-rotation failure.

## 10. Explain CRI, CNI, and CSI.

- **CRI:** kubelet-to-container-runtime interface. It covers sandbox creation, image pulls, container lifecycle, status, and logs.
- **CNI:** Network interface used to attach Pod sandboxes to networks and assign addressing/routes.
- **CSI:** Storage interface used for provisioning, attachment, mount, resize, snapshots, and volume lifecycle.

```bash
# Runtime
crictl info
crictl ps -a
crictl pods

# CNI
ls -l /etc/cni/net.d
ls -l /opt/cni/bin
ip link
ip route

# CSI
kubectl get csidrivers
kubectl get csinodes
kubectl get volumeattachments
kubectl get pods -A | grep -i csi
```

Kubernetes defines interfaces, but plugins implement behavior. Troubleshooting must include the actual runtime, CNI, CSI, and vendor version.

## 11. What is a Pod sandbox?

A Pod sandbox is the runtime-level environment that provides shared namespaces and networking for containers in one Pod. A pause or infrastructure container often holds the network namespace while application containers join it.

Containers in a Pod normally share:

- Pod IP and network namespace.
- Loopback connectivity and port space.
- Declared volumes.
- Optionally a process namespace when enabled.

```bash
crictl pods
crictl inspectp <sandbox-id>
crictl ps --pod <sandbox-id>
```

If sandbox creation fails, Pods often remain `ContainerCreating`. Common causes include CNI errors, exhausted Pod IPs, missing CNI binaries, runtime problems, SELinux/filesystem issues, or stale network state.

## 12. Explain Pod phases, conditions, and container states.

Pod phases:

- **Pending:** Accepted, but one or more containers are not running.
- **Running:** Bound to a node and at least one container is running, starting, or restarting.
- **Succeeded:** All containers completed successfully.
- **Failed:** All containers terminated and at least one failed.
- **Unknown:** Status could not be obtained.

Container states are more precise: `Waiting`, `Running`, and `Terminated`. Conditions such as `PodScheduled`, `Initialized`, `ContainersReady`, and `Ready` are often more useful than phase.

```bash
kubectl get pod <pod> -o wide
kubectl get pod <pod> -o jsonpath='{.status.phase}{"\n"}'
kubectl get pod <pod> -o jsonpath='{range .status.conditions[*]}{.type}={.status}{" "}{.reason}{"\n"}{end}'
kubectl describe pod <pod>
kubectl logs <pod> -c <container> --previous
```

`Terminating` is generally a display state caused by `deletionTimestamp`; it is not an official Pod phase.

## 13. What happens when a Pod is deleted?

Typical graceful flow:

1. The API object receives a deletion timestamp.
2. Traffic state starts changing through readiness and EndpointSlice updates.
3. Kubelet begins termination.
4. `preStop` runs if configured.
5. The runtime sends the termination signal, normally SIGTERM.
6. Kubernetes waits for `terminationGracePeriodSeconds`.
7. Remaining processes receive SIGKILL.
8. Volumes unmount and the sandbox is removed.
9. Finalizers complete and the API object disappears.

```bash
kubectl get pod <pod> -o jsonpath='{.metadata.deletionTimestamp}'
kubectl get pod <pod> -o jsonpath='{.metadata.finalizers}'
```

`kubectl delete pod --force --grace-period=0` removes the API object immediately but does not guarantee that a process on an unreachable node has stopped. Duplicate execution can occur.

## 14. Differentiate liveness, readiness, and startup probes.

- **Startup:** Determines whether slow initialization has completed. Liveness/readiness wait until it succeeds.
- **Liveness:** Determines whether kubelet should restart the container.
- **Readiness:** Determines whether the Pod should receive Service traffic. Failure does not restart the container.

```yaml
startupProbe:
  httpGet: {path: /startup, port: 8080}
  periodSeconds: 5
  failureThreshold: 60
readinessProbe:
  httpGet: {path: /ready, port: 8080}
  periodSeconds: 5
livenessProbe:
  httpGet: {path: /live, port: 8080}
  periodSeconds: 10
```

Common mistakes:

- Liveness depends on a database and causes restart storms.
- Readiness becomes true before cache warm-up.
- Timeouts are shorter than normal loaded response time.
- Exec probes create excessive process overhead.
- The same endpoint is used without correct semantics.

A probe should represent the intended local application state, not merely “port open.”

## 15. What does `CrashLoopBackOff` mean?

It means a container repeatedly starts and exits, and kubelet is applying increasing restart delay. It is a symptom, not a root cause.

```bash
kubectl describe pod <pod>
kubectl logs <pod> -c <container>
kubectl logs <pod> -c <container> --previous
kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[*].lastState}'
```

Check exit code, command/arguments, missing configuration, permissions, OOM, probes, dependencies, CPU architecture, read-only filesystems, and writable paths.

Common exit codes:

- 0: Process completed but a Deployment expected a long-running process.
- 1: Generic application error.
- 126: Command not executable.
- 127: Command not found.
- 137: Often SIGKILL, commonly OOM or forced termination.
- 143: SIGTERM, often graceful termination.

Do not fix CrashLoopBackOff by merely increasing probe thresholds.

## 16. How do you debug a minimal image with no shell or tools?

Options:

```bash
kubectl debug -it pod/<pod> --image=busybox:stable --target=<container>
kubectl debug pod/<pod> -it --copy-to=<pod>-debug --image=ubuntu
kubectl debug node/<node> -it --image=ubuntu
```

Also use object status, events, logs, and node-level runtime tools:

```bash
crictl ps
crictl inspect <container-id>
crictl logs <container-id>
```

Ephemeral and node-debug access must be RBAC-controlled and audited. Privileged debug Pods can expose host data. Interactive changes are not persistent and must not replace a corrected image build.

# 3. Workload Controllers and Application Operations

## 17. Compare Deployment, StatefulSet, DaemonSet, Job, and CronJob.

**Deployment:** Stateless, replaceable replicas; rolling update and rollback through ReplicaSets.

**StatefulSet:** Stable ordinal identity and storage association; often uses a headless Service. It does not automatically provide database replication or fencing.

**DaemonSet:** One Pod on all or selected nodes; used for CNI, logging, monitoring, and storage agents.

**Job:** Runs work to completion with retry and parallelism controls.

**CronJob:** Creates Jobs on a schedule; requires careful concurrency, deadlines, history, idempotency, and time-zone design.

Controller selection must follow application semantics. A StatefulSet does not by itself make a database highly available.

## 18. How does a Deployment rolling update work?

A Deployment creates a new ReplicaSet for the new Pod template and gradually scales it up while scaling the old ReplicaSet down.

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

- `maxUnavailable`: Maximum desired replicas unavailable.
- `maxSurge`: Additional Pods allowed above desired count.
- Readiness determines availability.
- `minReadySeconds` requires sustained readiness.
- `progressDeadlineSeconds` identifies a stalled rollout.

```bash
kubectl rollout status deployment/web
kubectl rollout history deployment/web
kubectl rollout undo deployment/web
kubectl get rs -l app=web
```

Rollback restores a previous Pod template, not database migrations, external configuration, or dependent-service changes.

## 19. Why can a Deployment be available while users still see failures?

Kubernetes availability conditions do not prove end-to-end service health. Causes include:

- Weak readiness checks.
- Wrong Service selector or targetPort.
- EndpointSlice issues.
- Ingress/Gateway errors.
- NetworkPolicy.
- Version incompatibility during mixed rollout.
- Dependency failure after readiness.
- Session affinity to one bad replica.
- TLS, DNS, or external load-balancer problems.

```bash
kubectl get deploy,rs,pod -l app=web -o wide
kubectl get svc web -o yaml
kubectl get endpointslice -l kubernetes.io/service-name=web -o yaml
kubectl exec <client-pod> -- curl -sv http://web:8080/health
kubectl logs -l app=web --all-containers --since=10m
```

Use request rate, error rate, latency, saturation, dependency telemetry, and synthetic transactions.

## 20. What are owner references and garbage collection?

Owner references relate dependent objects to owners. A Deployment owns ReplicaSets; a ReplicaSet owns Pods. Garbage collection can delete dependents when the owner is deleted.

```bash
kubectl get pod <pod> -o jsonpath='{.metadata.ownerReferences}'
kubectl delete deployment web --cascade=foreground
kubectl delete deployment web --cascade=orphan
```

Deletion modes:

- **Foreground:** Owner remains until blocking dependents are deleted.
- **Background:** Owner is removed and dependents are deleted asynchronously.
- **Orphan:** Dependents remain.

Finalizers and operators can alter cleanup behavior. Orphaning may leave costly or insecure resources.

## 21. What are finalizers, and how do you handle an object stuck in Terminating?

A finalizer tells Kubernetes to delay final deletion until a controller performs cleanup.

```bash
kubectl get <kind> <name> -o yaml
kubectl get <kind> <name> -o jsonpath='{.metadata.finalizers}'
```

Safe process:

1. Identify the finalizer owner.
2. Check controller and webhook logs.
3. Restore controller connectivity or credentials.
4. Verify dependent and external resources.
5. Let the controller remove the finalizer.
6. Manually remove only after accepting skipped cleanup.

```bash
kubectl patch <kind> <name> --type=merge -p '{"metadata":{"finalizers":[]}}'
```

Manual removal can orphan load balancers, disks, snapshots, DNS records, cloud identities, or custom resources.

## 22. How do you design a reliable CronJob?

```yaml
apiVersion: batch/v1
kind: CronJob
spec:
  schedule: "0 2 * * *"
  concurrencyPolicy: Forbid
  startingDeadlineSeconds: 1800
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 5
  jobTemplate:
    spec:
      backoffLimit: 2
      ttlSecondsAfterFinished: 86400
      template:
        spec:
          restartPolicy: Never
```

Requirements:

- Idempotent work because retries/duplicates can occur.
- Distributed lock or unique business key for exactly-once semantics.
- Conscious `Allow`, `Forbid`, or `Replace` choice.
- Realistic deadline and retry behavior.
- Requests/limits and appropriate priority.
- Monitoring of missed schedules, duration, failures, and backlog.
- Durable output and secure credentials.

```bash
kubectl describe cronjob <name>
kubectl create job --from=cronjob/<name> <name>-manual
```

## 23. What is a headless Service and when is it used?

A headless Service sets:

```yaml
spec:
  clusterIP: None
```

Instead of returning a single virtual IP, DNS can return Pod endpoint addresses. It is used for StatefulSets, peer discovery, and applications that perform client-side load balancing.

A headless Service does not itself provide application clustering, quorum, or health-aware client behavior beyond the available endpoint information.

## 24. How do you pause and safely recover a failed rollout?

```bash
kubectl rollout pause deployment/<name>
kubectl describe deployment <name>
kubectl get rs -l app=<label>
kubectl get pods -l app=<label> -o wide
kubectl rollout history deployment/<name>
```

Then decide whether to fix forward or roll back:

```bash
kubectl rollout undo deployment/<name>
kubectl rollout resume deployment/<name>
```

Check schema migrations, ConfigMap/Secret changes, compatibility with older replicas, external dependencies, PDBs, and capacity before rollback.

## 25. How do readiness gates differ from normal readiness probes?

A readiness probe is evaluated by kubelet. A **readiness gate** adds custom Pod conditions that an external controller must set. A Pod is Ready only when normal container readiness is true and all configured readiness-gate conditions are true.

Use cases include cloud load-balancer registration, external approval, or sidecar/controller-managed readiness. A missing controller or never-updated condition can leave Pods permanently not ready, so the dependency must be monitored and highly available.


# 4. Services, DNS, CNI, Ingress, and Gateway API

## 26. Explain the Kubernetes networking model.

The baseline model expects every Pod to receive an IP address, Pods to communicate with other Pods, nodes to reach Pods, and containers in one Pod to communicate over localhost. Services provide stable discovery and virtual addressing for changing Pod backends.

The CNI implementation supplies the real data plane. It may use overlays, native routing, eBPF, cloud VPC integration, or a combination. NetworkPolicy support is also CNI-dependent.

Troubleshoot layer by layer:

1. Container process and listening socket.
2. Pod network namespace.
3. Pod-to-Pod routing.
4. Service VIP and backend selection.
5. DNS.
6. NetworkPolicy.
7. Ingress/Gateway/load balancer.
8. External firewall, routing, MTU, and TLS.

```bash
kubectl get pods -o wide
kubectl get nodes -o wide
kubectl get svc,endpointslice
kubectl exec <pod> -- ip addr
kubectl exec <pod> -- ip route
kubectl exec <pod> -- cat /etc/resolv.conf
```

Do not assume all CNIs use identical interfaces, iptables rules, or troubleshooting commands.

## 27. Explain Kubernetes Service types.

- **ClusterIP:** Stable internal virtual IP and DNS name.
- **NodePort:** Opens a port on nodes and forwards to the Service.
- **LoadBalancer:** Requests a provider-integrated load balancer.
- **ExternalName:** Returns a DNS CNAME for an external name; Kubernetes does not proxy it.

A headless Service uses `clusterIP: None` and returns backend endpoint addresses.

```bash
kubectl get svc <name> -o wide
kubectl describe svc <name>
kubectl get endpointslice -l kubernetes.io/service-name=<name> -o yaml
```

L3 considerations include source-IP preservation, internal/public load balancers, `externalTrafficPolicy`, dual-stack, health checks, session affinity, topology-aware routing, and TLS ownership.

## 28. How does Service traffic reach Pods?

The API server stores Service and EndpointSlice objects. A service data-plane implementation watches them and programs forwarding behavior. Traditional clusters may use kube-proxy with iptables or IPVS; some CNIs replace kube-proxy with eBPF.

```text
Client -> Service VIP:port -> ready endpoint -> Pod IP:targetPort
```

```bash
kubectl get svc <svc> -o yaml
kubectl get endpointslice -l kubernetes.io/service-name=<svc> -o wide
kubectl get pod -l <selector> --show-labels -o wide
kubectl exec <debug-pod> -- curl -sv http://<service>:<port>
kubectl exec <debug-pod> -- curl -sv http://<pod-ip>:<target-port>
```

Interpretation:

- Pod IP fails: application, Pod networking, NetworkPolicy, or node path.
- Pod IP works but Service VIP fails: Service definition or service data plane.
- Service works internally but not externally: load balancer, firewall, ingress, routing, health checks, or TLS.

## 29. What are EndpointSlices, and why are they important?

EndpointSlices represent groups of Service backends and scale better than one large legacy Endpoints object. They can record address type, port, readiness, serving, termination state, node, and zone hints.

```bash
kubectl get endpointslices -A
kubectl get endpointslice \
  -l kubernetes.io/service-name=<service> \
  -n <namespace> -o yaml
```

Verify addresses, ports, readiness, address family, terminating state, labels, and ownership. A Service can resolve in DNS while having no ready endpoints.

## 30. How does Kubernetes DNS work?

CoreDNS normally watches the Kubernetes API and serves records such as:

```text
service.namespace.svc.cluster.local
```

A Pod resolver often contains:

```text
search <namespace>.svc.cluster.local svc.cluster.local cluster.local
nameserver <cluster-dns-service-ip>
options ndots:5
```

Troubleshooting:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns -o wide
kubectl get svc -n kube-system kube-dns -o wide
kubectl logs -n kube-system -l k8s-app=kube-dns --since=10m
kubectl exec <pod> -- cat /etc/resolv.conf
kubectl exec <pod> -- nslookup kubernetes.default.svc.cluster.local
```

Common failures include CoreDNS saturation, Service-data-plane issues, blocked UDP/TCP 53, wrong `clusterDNS`, upstream timeout, `ndots` query amplification, conntrack exhaustion, and MTU problems.

## 31. Explain NetworkPolicy behavior and common mistakes.

NetworkPolicy selects Pods and defines allowed ingress/egress at L3/L4. It works only when the CNI implements it.

Important behavior:

- Policies are additive.
- A Pod is not isolated in a direction until selected by a policy declaring that direction.
- Standard NetworkPolicy has no explicit deny rule.
- It does not normally filter HTTP paths or identities.
- DNS and required platform endpoints must be explicitly allowed after egress isolation.

Default deny:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
  namespace: app
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -A
kubectl get pod -n app --show-labels
kubectl exec -n app <source> -- nc -vz <destination> <port>
```

Common mistakes are wrong labels, forgotten DNS, incorrect selector indentation, and testing from a Pod not selected by the policy.

## 32. Compare Ingress and Gateway API.

Ingress is a stable HTTP/HTTPS routing API, but advanced behavior often relies on controller-specific annotations.

Gateway API is an add-on family of role-oriented, protocol-aware APIs such as GatewayClass, Gateway, and HTTPRoute. It separates infrastructure ownership from application route ownership and supports richer delegation.

```bash
kubectl get ingress -A
kubectl describe ingress <name> -n <ns>
kubectl get gatewayclass,gateway -A
kubectl get httproute -A
kubectl describe gateway <name> -n <ns>
kubectl describe httproute <name> -n <ns>
```

An API object does nothing without a compatible controller. Inspect status conditions, route acceptance, backend references, TLS Secrets, controller logs, Service endpoints, external DNS, and load-balancer state.

## 33. How do you troubleshoot an MTU mismatch?

Overlay encapsulation lowers effective MTU. Symptoms include small requests working while large responses, TLS, gRPC, or cross-node traffic fails.

```bash
ip link
kubectl exec <pod> -- ip link
ping -M do -s 1400 <destination>
tcpdump -ni any host <ip>
```

Compare same-node and cross-node traffic. Inspect underlay, tunnel, Pod-interface, VPN, and cloud-provider MTUs. Fix the design consistently rather than applying arbitrary MSS clamping first.

## 34. What is `externalTrafficPolicy`, and why does it matter?

For NodePort or LoadBalancer Services:

- `Cluster` allows forwarding to endpoints across the cluster and may hide the original client IP.
- `Local` sends traffic only to local-node endpoints, often preserving client IP and avoiding an extra hop, but nodes without local endpoints may fail health checks or drop traffic.

The correct choice depends on source-IP requirements, traffic balance, topology, load-balancer health checks, and failure handling.

```bash
kubectl get svc <svc> -o jsonpath='{.spec.externalTrafficPolicy}{"\n"}'
```

## 35. How do you troubleshoot cross-node Pod communication failure?

1. Verify same-node communication.
2. Verify Pod IPs and node placement.
3. Check node routes, tunnel interfaces, and CNI agents.
4. Check firewall rules and security groups.
5. Validate MTU.
6. Check BGP/overlay/eBPF status using CNI-specific tools.
7. Inspect packet drops and captures.
8. Compare one source node and one destination node to limit scope.

```bash
kubectl get pods -o wide
kubectl get nodes -o wide
ip route
ip link
journalctl -u kubelet
```

Avoid restarting all CNI agents at once; it may widen the outage.

# 5. Scheduling, Resources, Autoscaling, and Eviction

## 36. Explain the scheduler workflow.

The scheduler broadly performs:

1. Queueing.
2. Pre-filter/filter of hard constraints.
3. Scoring of feasible nodes.
4. Reservation and optional permit stages.
5. Binding by setting the selected node.
6. Post-bind work.

```bash
kubectl describe pod <pod>
kubectl get events --field-selector involvedObject.name=<pod>
kubectl get nodes --show-labels
kubectl describe node <node>
```

Typical messages:

- `Insufficient cpu`
- `Insufficient memory`
- `untolerated taint`
- `didn't match node affinity`
- `unbound immediate PersistentVolumeClaims`
- `volume node affinity conflict`
- `preemption is not helpful`

Do not solve every Pending Pod by adding nodes. Identify requests, conflicting constraints, storage topology, quota, and priorities.

## 37. Explain CPU and memory requests and limits.

A request influences scheduling and guarantees. A limit is the enforceable maximum.

CPU:

- Measured in cores or millicores.
- Exceeding a limit causes cgroup throttling, not OOM.
- Low limits can increase latency even with idle node CPU.

Memory:

- Requests influence scheduling and QoS.
- Exceeding the memory limit can cause OOM termination.
- Memory is not compressible like CPU time.

```yaml
resources:
  requests:
    cpu: 500m
    memory: 512Mi
  limits:
    cpu: "2"
    memory: 1Gi
```

Base values on measured steady, percentile, and startup usage. Include sidecars and monitor throttling, OOMs, working set, RSS, and node pressure.

## 38. Explain Kubernetes QoS classes.

- **Guaranteed:** Every container has CPU and memory request/limit, and request equals limit.
- **Burstable:** At least one request/limit exists, but Guaranteed criteria are not met.
- **BestEffort:** No CPU or memory requests/limits.

```bash
kubectl get pod <pod> -o jsonpath='{.status.qosClass}{"\n"}'
```

QoS influences cgroup configuration and eviction preference, but Guaranteed Pods can still hit their memory limit, fail probes, or be evicted in some circumstances.

## 39. Compare nodeSelector, affinity, anti-affinity, and topology spread.

- `nodeSelector`: Simple exact node-label matching.
- Node affinity: Required/preferred expressive node rules.
- Pod affinity: Co-locates Pods near selected Pods.
- Pod anti-affinity: Separates selected Pods.
- Topology spread: Distributes matching Pods across domains with controlled skew.

Hard anti-affinity can leave workloads Pending after cluster-size or label changes. Topology spread and preferred rules often offer better resilience.

```bash
kubectl get nodes --show-labels
kubectl describe pod <pod>
```

Consider failure domains, storage topology, autoscaling, maintenance, and rollout surge capacity.

## 40. Explain taints and tolerations.

Taints repel Pods unless they tolerate them.

- `NoSchedule`: New non-tolerating Pods are not scheduled.
- `PreferNoSchedule`: Scheduler tries to avoid the node.
- `NoExecute`: New Pods are rejected and existing Pods may be evicted.

```bash
kubectl taint nodes worker-1 dedicated=payments:NoSchedule
kubectl taint nodes worker-1 dedicated=payments:NoSchedule-
kubectl describe node worker-1
```

A toleration permits scheduling; it does not force placement. Combine with node affinity for dedicated nodes. Avoid broad `operator: Exists` tolerations that unintentionally allow workloads onto unhealthy or privileged nodes.

## 41. What are PriorityClasses and preemption?

PriorityClass assigns integer priority. If a high-priority Pod cannot schedule, the scheduler may preempt lower-priority Pods when doing so makes scheduling possible.

```bash
kubectl get priorityclass
kubectl get pod <pod> -o jsonpath='{.spec.priorityClassName}{" "}{.spec.priority}{"\n"}'
```

Use a governed set of classes, reserve highest values for critical platform components, monitor starvation, and prevent abuse. Preemption cannot fix impossible affinity, missing tolerations, or volume topology conflicts.

## 42. Explain node-pressure eviction.

Kubelet monitors available memory, node/image filesystem space, and inodes. Threshold breaches can trigger reclaim and Pod eviction.

```bash
kubectl describe node <node>
kubectl get events -A --sort-by=.metadata.creationTimestamp
journalctl -u kubelet --since '30 min ago'
df -h
df -i
```

Common conditions include `MemoryPressure`, `DiskPressure`, and `PIDPressure`. Remediation may include fixing leaks, log rotation, ephemeral-storage sizing, image cleanup, capacity expansion, or workload movement.

Never blindly delete `/var/lib/kubelet` or runtime data.

## 43. What is a PodDisruptionBudget, and what does it not protect against?

A PDB limits **voluntary disruptions** using `minAvailable` or `maxUnavailable`.

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: web-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: web
```

It affects eviction-based maintenance such as drain. It does not guarantee protection from node crash, OOM, application failure, all pressure evictions, direct deletion, or zone loss.

```bash
kubectl get pdb -A
kubectl describe pdb web-pdb
```

Bad PDB design can block maintenance indefinitely.

## 44. Compare HPA, VPA, and node/cluster autoscaling.

- **HPA:** Changes replica count using CPU, memory, custom, or external metrics.
- **VPA:** Recommends or applies resource-request changes; may recreate Pods.
- **Node autoscaling:** Adds/removes node capacity based on unschedulable Pods and utilization.

Consider metric lag, startup spikes, stabilization, cold start, PDBs, topology, provisioning delay, cloud limits, and dependency capacity. Autoscaling cannot solve impossible scheduling constraints.

```bash
kubectl get hpa -A
kubectl describe hpa <name>
kubectl top pod
```

## 45. Why does HPA often behave badly when requests are wrong?

CPU utilization targets are commonly calculated relative to CPU requests. If requests are too low, ordinary usage appears as very high utilization and HPA over-scales. If requests are too high, HPA may under-scale.

Missing metrics, short spikes, slow startup, metric delay, and conflicting VPA changes can also destabilize scaling. Use measured requests, stabilization windows, scaling policies, and application-level metrics when CPU does not represent load.

## 46. What is `ephemeral-storage`, and why should it be managed?

Node-local writable layers, container logs, and `emptyDir` volumes can consume ephemeral storage. Requests and limits can be set similarly to CPU/memory.

Unbounded use can create DiskPressure, evictions, image-pull failures, and node instability.

```yaml
resources:
  requests:
    ephemeral-storage: 1Gi
  limits:
    ephemeral-storage: 5Gi
```

Monitor bytes and inodes, configure log rotation, control application scratch data, and understand which filesystem kubelet/runtime use.

## 47. What is CPU throttling, and how do you prove it?

When a container reaches its cgroup CPU bandwidth limit, it can be throttled even if the node has idle CPU. Symptoms include latency spikes with apparently moderate average CPU.

Use workload metrics for throttled periods/time and node-level cgroup data. Compare requests, limits, actual CPU, run queue, and application latency. A safe fix may be increasing/removing an overly restrictive CPU limit, reducing concurrency, or scaling horizontally—after capacity validation.

## 48. How do hugepages differ from normal memory resources?

Hugepages are explicitly requested resources and are not overcommitted like normal memory. The node must preallocate the relevant hugepage size, and Pods request resources such as `hugepages-2Mi`.

Scheduling fails if the requested hugepages are not available. Hugepage configuration often requires node boot/runtime settings and careful NUMA placement. Memory limits and hugepage limits are separate.

## 49. How does topology-aware scheduling interact with storage?

Storage volumes can have node or zone affinity. A Pod may satisfy CPU and node affinity but fail because its volume is available only in another zone.

`WaitForFirstConsumer` StorageClass binding lets the scheduler consider Pod placement before provisioning. Align node labels, allowed topologies, zone capacity, StatefulSet spreading, and DR design.

```bash
kubectl describe pod <pod>
kubectl get pv <pv> -o yaml
kubectl get storageclass <sc> -o yaml
```

## 50. How do you safely change resource requests in production?

1. Review historical usage and startup peaks.
2. Include every container and sidecar.
3. Check node and namespace headroom.
4. Assess HPA/VPA interactions.
5. Stage the change in a lower environment or small canary.
6. Watch Pending Pods, rollout surge, throttling, OOM, latency, and cost.
7. Maintain rollback values.
8. Update quota and capacity plans if needed.

Do not bulk-change requests across the cluster without understanding scheduling and autoscaling consequences.


# 6. Persistent Storage, CSI, Backup, and Recovery

## 51. Explain PV, PVC, StorageClass, and dynamic provisioning.

A **PersistentVolume** is a cluster-scoped storage resource. A **PersistentVolumeClaim** is a namespaced request. A **StorageClass** defines a storage class, provisioner, and driver-specific parameters.

Dynamic flow:

1. User creates a PVC.
2. External CSI provisioner observes it.
3. The backend creates storage.
4. A PV is created and bound.
5. During Pod startup, attachment and mount operations occur.
6. Kubelet publishes the volume into the Pod.

```bash
kubectl get pvc -A
kubectl get pv
kubectl get storageclass
kubectl describe pvc <pvc> -n <ns>
kubectl get volumeattachments
kubectl get pods -A | grep -i csi
```

PVC binding success does not prove node attachment or mount success.

## 52. Compare `Immediate` and `WaitForFirstConsumer` binding.

`Immediate` provisions/binds as soon as the PVC is created. With zonal storage, it may create a volume in a zone that conflicts with later Pod placement.

`WaitForFirstConsumer` delays provisioning or binding until the scheduler evaluates the first Pod, allowing node affinity, zone, and topology constraints to influence placement.

```bash
kubectl get storageclass <sc> -o yaml
kubectl describe pvc <pvc>
kubectl describe pod <pod>
```

A common failure is `volume node affinity conflict`.

## 53. Explain access modes and volume modes.

Access modes:

- `ReadWriteOnce` (RWO): Read-write from one node.
- `ReadOnlyMany` (ROX): Read-only from many nodes.
- `ReadWriteMany` (RWX): Read-write from many nodes.
- `ReadWriteOncePod` (RWOP): Read-write by one Pod when supported.

Volume modes:

- `Filesystem`
- `Block`

RWO does not always mean one Pod; multiple Pods on the same node may use it depending on driver and application behavior. Use RWOP for strict single-Pod access when supported.

## 54. Explain PV reclaim policies.

- **Delete:** Released dynamically provisioned storage is normally deleted.
- **Retain:** PV and backing storage remain for manual recovery, sanitization, or reuse.

```bash
kubectl get pv <pv> -o jsonpath='{.spec.persistentVolumeReclaimPolicy}{"\n"}'
kubectl patch pv <pv> -p '{"spec":{"persistentVolumeReclaimPolicy":"Retain"}}'
```

`Retain` is not a backup. It does not protect against corruption, account compromise, region loss, or administrator error.

## 55. How do you troubleshoot a PVC stuck in Pending?

```bash
kubectl describe pvc <pvc> -n <ns>
kubectl get storageclass
kubectl get storageclass <sc> -o yaml
kubectl get events -n <ns> --sort-by=.metadata.creationTimestamp
kubectl get pods -A | grep -i csi
```

Possible causes:

- Missing or wrong StorageClass.
- No default class.
- Provisioner unavailable.
- Invalid driver parameters.
- Backend capacity/quota exhausted.
- Invalid credentials.
- Unsupported access mode or size.
- Topology cannot be satisfied.
- `WaitForFirstConsumer` has no consumer.
- Snapshot/data-source failure.

If PVC is Bound but Pod is stuck, inspect VolumeAttachments, CSI node Pods, kubelet logs, and mount events.

## 56. How do you troubleshoot a Multi-Attach error?

A single-writer volume may still be attached to another node or reported as attached.

```bash
kubectl describe pod <pod>
kubectl get volumeattachments
kubectl get volumeattachment <name> -o yaml
kubectl get pod -A -o wide | grep <app>
```

Safe response:

1. Confirm the old Pod/process is stopped.
2. Restore old-node connectivity if possible and allow normal detach.
3. Check CSI controller and backend state.
4. Fence the old node.
5. Use provider force-detach only after proving no active writer.
6. Validate filesystem and application consistency.

Availability must not override data integrity.

## 57. What is the CSI attachment and mount sequence?

A typical block-volume path is:

1. Provision volume.
2. Bind PVC/PV.
3. Scheduler selects a compatible node.
4. ControllerPublish attaches the volume to the node.
5. NodeStage stages it on the node.
6. NodePublish mounts or exposes it to the Pod.
7. On deletion, NodeUnpublish/NodeUnstage occur.
8. ControllerUnpublish detaches it.

Errors at each stage produce different events. Separate provisioning, binding, attachment, staging, publishing, filesystem, and application failures.

## 58. Are Kubernetes objects and etcd snapshots enough for application backup?

No. A complete recovery plan may require:

- API objects and CRDs.
- etcd snapshot.
- Persistent-volume data.
- External databases, queues, object storage, DNS, and load balancers.
- Encryption/KMS keys.
- Registry images.
- Operator-specific procedures.
- Restore ordering and application consistency.

Crash-consistent snapshots may not be database-consistent. Define RPO, RTO, retention, immutability, encryption, offsite copies, and regular restore testing.

## 59. What are VolumeSnapshots?

VolumeSnapshot APIs request storage snapshots through CSI. Common resources include VolumeSnapshot, VolumeSnapshotContent, and VolumeSnapshotClass.

```bash
kubectl get volumesnapshot -A
kubectl get volumesnapshotcontent
kubectl get volumesnapshotclass
kubectl describe volumesnapshot <name> -n <ns>
```

Verify driver support, ready status, deletion policy, restore to a new PVC, application quiescing, failure-domain placement, retention, and credential protection. A snapshot in the same compromised account is not necessarily ransomware-resilient.

## 60. How do you protect storage during node maintenance?

- Cordon and drain with sufficient grace.
- Verify application shutdown and Pod termination.
- Confirm detach for single-writer volumes.
- Respect PDB and StatefulSet ordering.
- Do not force-delete Pods with mounted write volumes unless fencing is understood.
- Validate CSI controllers and node plugins.
- Verify reattachment and data consistency after maintenance.
- Keep storage-provider runbooks and escalation contacts.

# 7. Authentication, RBAC, Admission, and Security

## 61. Explain authentication, authorization, and admission.

1. **Authentication:** Who is the caller?
2. **Authorization:** May the caller perform this action?
3. **Admission:** Is this exact request acceptable, and should it be mutated?

```bash
kubectl auth whoami
kubectl auth can-i get pods -n app
kubectl auth can-i --list -n app
kubectl get validatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations
```

HTTP interpretation:

- 401: Authentication failure.
- 403: Authenticated but forbidden.
- 400/422: Invalid request/validation failure.
- Webhook problems may appear as denial, TLS error, timeout, or internal error.

## 62. Explain Role, ClusterRole, RoleBinding, and ClusterRoleBinding.

- **Role:** Namespaced rule definition.
- **ClusterRole:** Cluster-scoped rule definition that can also contain namespaced-resource rules.
- **RoleBinding:** Grants Role or ClusterRole permissions within one namespace.
- **ClusterRoleBinding:** Grants a ClusterRole cluster-wide.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: app
rules:
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "list", "watch"]
```

```bash
kubectl auth can-i list pods -n app --as=user@example.com
kubectl get role,rolebinding -n app
kubectl get clusterrole,clusterrolebinding
```

RBAC is additive and has no standard deny rule. Secret access, exec, port-forward, impersonation, node proxy, token creation, and workload creation are sensitive permissions.

## 63. Why is permission to create Pods highly sensitive?

A user who can create Pods may be able to:

- Run arbitrary tools.
- Use a namespace service account.
- Mount Secrets/ConfigMaps.
- Request host access or privileges if admission allows.
- Reach internal services.
- Use projected tokens to call the API.
- Exfiltrate data through egress.

Secure multi-tenancy therefore combines RBAC, Pod Security Admission, policy, NetworkPolicy, service-account control, image controls, runtime hardening, quotas, and audit logging.

## 64. How should service accounts be managed?

Use a dedicated service account per workload and least-privilege RBAC.

```yaml
spec:
  serviceAccountName: payments-api
  automountServiceAccountToken: false
```

Disable token mounting when the workload does not call the API. Prefer short-lived projected bound tokens. Avoid shared broad accounts and cluster-admin bindings.

```bash
kubectl auth can-i --list \
  --as=system:serviceaccount:app:payments-api \
  -n app
```

Kubernetes RBAC and cloud workload identity must be reviewed together.

## 65. What is admission control, and how can a webhook outage affect the cluster?

Admission runs after authentication/authorization and before persistence. Mutating webhooks can change objects; validating webhooks can reject them.

Important settings:

- `failurePolicy`
- `timeoutSeconds`
- selectors and match conditions
- CA bundle and service reference
- rules and side-effect declarations

```bash
kubectl get mutatingwebhookconfigurations -o yaml
kubectl get validatingwebhookconfigurations -o yaml
kubectl get endpointslice -A | grep <webhook-service>
```

`Fail` preserves enforcement but can block changes during outage. `Ignore` favors availability but can bypass policy. Design webhooks with HA replicas, topology spread, PDB, certificate rotation, monitoring, and a break-glass runbook.

## 66. Explain Pod Security Standards and Pod Security Admission.

Levels:

- **Privileged:** Unrestricted.
- **Baseline:** Prevents known privilege-escalation patterns.
- **Restricted:** Stronger current hardening practices.

Namespace labels can enforce, audit, and warn:

```bash
kubectl label namespace app \
  pod-security.kubernetes.io/enforce=restricted \
  pod-security.kubernetes.io/enforce-version=latest \
  pod-security.kubernetes.io/audit=restricted \
  pod-security.kubernetes.io/warn=restricted
```

Roll out with audit/warn first, remediate workloads, restrict who can change labels, then enforce. PodSecurityPolicy was removed in v1.25 and is obsolete.

## 67. What securityContext settings do you expect in a hardened Pod?

```yaml
securityContext:
  runAsNonRoot: true
  seccompProfile:
    type: RuntimeDefault
containers:
- name: app
  securityContext:
    allowPrivilegeEscalation: false
    readOnlyRootFilesystem: true
    capabilities:
      drop: ["ALL"]
```

Also consider explicit UID/GID, SELinux/AppArmor, no privileged mode, no host namespaces, minimal capabilities, writable `emptyDir` for required paths, and safe volume ownership.

Do not set an arbitrary UID without validating image and volume permissions.

## 68. How are Kubernetes Secrets protected, and what are their limitations?

Secret data is base64-encoded in manifests, not encrypted by base64.

Protection layers:

- TLS in transit.
- RBAC least privilege.
- Encryption at rest and optional external KMS.
- Restricted etcd and backup access.
- Audit logging.
- Rotation and short-lived credentials.
- Avoiding secret values in logs, images, and CI output.

Encryption at rest does not stop an authorized user or Pod from reading decrypted content.

## 69. Explain encryption at rest and key rotation.

API-server encryption configuration can encrypt selected resources before etcd storage. Provider order affects writes and reads.

Safe rotation:

1. Add new key/provider as primary while retaining old decrypt capability.
2. Apply consistent configuration to all API servers.
3. Rewrite selected objects in a controlled manner.
4. Verify new ciphertext and application access.
5. Retire old key only after proof.
6. Back up configuration and KMS keys securely.

Losing all usable keys can make encrypted data unrecoverable. Rewriting many resources can create API/etcd load.

## 70. How do you secure the image supply chain?

- Trusted registries.
- Immutable digests for critical releases.
- Vulnerability scanning and risk triage.
- Signature verification and admission policy.
- SBOM generation and retention.
- Minimal patched base images.
- Reproducible controlled builds.
- No secrets in image layers.
- Non-root, dropped capabilities, runtime-default seccomp.
- Restricted image-pull credentials.
- Mirroring of critical images for DR.
- Approved, time-bound exceptions.

Policy systems must be highly available so security controls do not accidentally block all deployments.

## 71. What should Kubernetes audit logging capture?

Capture identity, verb, resource, namespace, response code, source, and correlation data. Alert on:

- RBAC changes.
- Secret access.
- exec and port-forward.
- impersonation.
- cluster-admin grants.
- namespace security-label changes.
- webhook and APIService changes.
- certificate-signing requests.

Avoid logging Secret bodies unnecessarily. Protect logs against tampering and centralize them. Audit logs show API activity, not all runtime processes or network traffic.

## 72. How do you secure kubelet access?

- Require authenticated kubelet API access.
- Use appropriate authorization, normally webhook/RBAC-based authorization.
- Disable anonymous access unless explicitly required.
- Protect serving/client certificates and rotate them.
- Restrict node and kubelet ports with network controls.
- Avoid broad access to node proxy and logs.
- Monitor certificate expiry and kubelet authentication failures.
- Limit host-level access because root on a node can usually affect workloads on that node.

## 73. How do you review RBAC privilege escalation paths?

Look beyond obvious `cluster-admin` bindings. Sensitive permissions include:

- Creating/updating RoleBindings or ClusterRoleBindings.
- `bind` and `escalate` verbs.
- Impersonation.
- Creating Pods with powerful service accounts.
- Reading Secrets.
- Creating token requests.
- Pod exec/attach/port-forward.
- Node proxy access.
- Modifying webhooks, CRDs, APIService, or namespace security labels.
- Approving CSRs.

Use `kubectl auth can-i`, policy analysis, and periodic binding reviews. Evaluate effective privilege chains, not only direct permissions.

## 74. What is the risk of `automountServiceAccountToken: true`?

The token becomes available inside the Pod and can be stolen through application compromise, vulnerable sidecars, debug access, or unintended file exposure. Even a low-privilege token provides cluster identity and may enable discovery or later privilege chains.

Disable automount for workloads that do not use the API. For those that do, minimize RBAC, use projected short-lived tokens, secure the filesystem, and monitor API activity.

## 75. How do you build a default-deny namespace securely?

A practical baseline includes:

- Default-deny ingress and egress NetworkPolicies.
- Explicit DNS egress.
- Explicit access to required dependencies.
- Restricted Pod Security Admission where compatible.
- Dedicated service accounts with token automount disabled by default.
- ResourceQuota and LimitRange.
- Controlled image sources.
- Namespace-owner RBAC without privilege-escalation paths.
- Audit and policy monitoring.

Test from representative source Pods because selectors, DNS, health checks, and platform agents can be unintentionally blocked.


# 8. Upgrades, Certificates, etcd, and Disaster Recovery

## 76. Describe a safe Kubernetes upgrade strategy.

A production upgrade is a controlled change.

Pre-checks:

- Confirm supported source and target versions.
- Do not skip unsupported minor versions.
- Review release notes, removed APIs, deprecations, and feature gates.
- Validate CNI, CSI, ingress/Gateway, metrics, policy, service mesh, operators, and device-plugin compatibility.
- Back up etcd and application data and test restoration.
- Confirm capacity for drain and rollout surge.
- Check PDBs, topology, certificates, disk, nodes, and monitoring.
- Freeze unrelated changes.

Execution:

1. Upgrade the control plane using the distribution-supported procedure.
2. Validate API, etcd, scheduler, controllers, DNS, CNI, admission, and critical workloads.
3. Drain and upgrade worker nodes in safe batches.
4. Monitor errors, latency, scheduling, and application SLOs.
5. Continue only after acceptance criteria pass.

A package downgrade is not automatically a safe rollback because etcd data and API conversions may have changed.

## 77. Explain Kubernetes version skew.

Version-skew rules define supported differences among kube-apiserver, kubelet, kube-proxy, kubectl, and HA API-server instances. Exact rules must be checked for the target release.

Operational principles:

- Upgrade API servers before nodes when required.
- Keep HA API servers within permitted skew.
- Keep kubelets supported relative to the API server.
- Use a supported kubectl version.
- Upgrade add-ons and CRDs using vendor matrices.
- Check removed APIs before changing the control plane.

```bash
kubectl version
kubectl get nodes -o custom-columns=NAME:.metadata.name,KUBELET:.status.nodeInfo.kubeletVersion,RUNTIME:.status.nodeInfo.containerRuntimeVersion
```

Maintain an automated compatibility matrix rather than depending on memory.

## 78. Outline a kubeadm cluster upgrade.

Typical sequence, adapted to the target release and OS:

1. Upgrade `kubeadm` on the first control-plane node.
2. Run:
   ```bash
   sudo kubeadm upgrade plan
   ```
3. Apply the selected target:
   ```bash
   sudo kubeadm upgrade apply v1.36.x
   ```
4. Upgrade kubelet/kubectl packages as required.
5. Restart kubelet and validate.
6. On additional control-plane nodes, use the supported node phase, commonly:
   ```bash
   sudo kubeadm upgrade node
   ```
7. Drain, upgrade, and uncordon workers.

Before execution:

```bash
kubectl get nodes
kubectl get pods -A
kubectl get --raw='/readyz?verbose'
sudo kubeadm certs check-expiration
```

Back up etcd independently and verify CNI compatibility.

## 79. How do you back up etcd in a kubeadm cluster?

Identify endpoint and certificate paths from the etcd static-Pod manifest, then create a snapshot:

```bash
export ETCDCTL_API=3
sudo etcdctl snapshot save /secure-backup/etcd-$(date +%F-%H%M).db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
  --key=/etc/kubernetes/pki/etcd/healthcheck-client.key
```

Validate using the supported `etcdutl` version:

```bash
sudo etcdutl snapshot status /secure-backup/etcd-YYYY-MM-DD-HHMM.db -w table
```

Store off-node, encrypt, checksum, protect access, record versions, retain encryption keys/config, monitor age, and test restore.

## 80. Explain a high-level etcd restore procedure.

1. Declare control-plane outage and stop conflicting writes.
2. Select and verify a snapshot.
3. Back up current manifests, PKI, and data directories.
4. Restore to a **new data directory** with correct member name, peer URLs, and cluster configuration.
5. Update the etcd static-Pod/service configuration.
6. Start members in the supported sequence.
7. Validate membership, leader, and endpoint health.
8. Validate API servers.
9. Validate scheduler, controllers, nodes, CNI, DNS, webhooks, CSI, and applications.
10. Reconcile external resources and changes after the recovery point.

Risks include wrong peer URLs, missing encryption keys, post-snapshot data loss, and controllers reconciling restored objects against newer external infrastructure.

## 81. How do you check kubeadm certificate expiry?

```bash
sudo kubeadm certs check-expiration
openssl x509 -in /etc/kubernetes/pki/apiserver.crt \
  -noout -subject -issuer -dates -ext subjectAltName
```

Monitor well in advance, understand which certificates kubeadm manages, renew with the supported procedure, restart/reload affected components, update kubeconfigs where needed, and validate every HA endpoint. Protect CA private keys.

## 82. How do you diagnose `x509: certificate has expired or is not yet valid`?

Identify the exact connection and certificate.

```bash
date -u
timedatectl
chronyc tracking
openssl s_client -connect <host>:6443 -showcerts </dev/null
openssl x509 -in <cert> -noout -issuer -subject -dates -ext subjectAltName
```

Causes include real expiry, clock skew, wrong certificate loaded, one unrenewed HA backend, expired client kubeconfig certificate, CA mismatch, or webhook/aggregated API certificate expiry.

Correct time first if wrong. Renew only the necessary chain, reload the component, validate all endpoints, and improve monitoring.

## 83. What is your disaster-recovery validation checklist?

Validate:

- API and etcd health.
- Node registration.
- CNI and Pod networking.
- DNS.
- CSI and volume attachment.
- Ingress/Gateway, external DNS, and load balancers.
- Identity, authentication, RBAC, and service accounts.
- Admission webhooks and policies.
- Secrets and encryption keys.
- Operators and custom resources.
- Persistent data integrity.
- External dependencies and registry availability.
- Monitoring, logging, alerting, and backup jobs.
- Real application transactions.
- Measured RTO and RPO.

Restoring objects is not enough; business services must be usable.

# 9. Monitoring, Logging, Auditing, and Capacity

## 84. What should be monitored in a production cluster?

**Control plane:** API availability/latency/errors, etcd leader and fsync/commit latency, scheduler latency, controller queues, admission latency.

**Nodes:** CPU, memory, PSI, load, disk latency, inodes, network drops, kubelet/runtime health, certificate expiry, reboots.

**Workloads:** desired versus ready replicas, restarts, OOMKills, evictions, probe failures, CPU throttling, request/error/latency/saturation.

**Add-ons:** CNI, DNS, CSI, ingress/Gateway, metrics, logging, policy, registry, and autoscaling.

**Business:** transaction success, queue depth, user latency, and data freshness.

A green Kubernetes object state does not guarantee application SLO success.

## 85. What is Metrics Server, and what are its limitations?

Metrics Server collects resource metrics from kubelets and exposes the resource metrics API used by `kubectl top` and commonly HPA.

```bash
kubectl top nodes
kubectl top pods -A
kubectl get apiservice v1beta1.metrics.k8s.io
kubectl describe apiservice v1beta1.metrics.k8s.io
```

It is not a long-term monitoring database, does not provide application metrics, and can fail because of kubelet certificates, addresses, network paths, or API aggregation issues. Do not use one `kubectl top` snapshot for capacity planning.

## 86. How should Kubernetes events be used?

Events provide context for scheduling, image pulls, volumes, probes, and controllers, but are not durable audit logs.

```bash
kubectl get events -A --sort-by=.metadata.creationTimestamp
kubectl get events -n app --field-selector involvedObject.name=<object>
kubectl describe pod <pod> -n app
```

Correlate with component logs, audit logs, node journal, runtime, cloud events, metrics, traces, and application logs. Check first/last timestamps, count, reason, source, and message.

## 87. How do you troubleshoot API-server latency?

1. Confirm scope: all clients, one backend, one resource, or one webhook.
2. Check `/readyz?verbose`.
3. Inspect API latency, inflight requests, response codes, and long-running requests.
4. Check etcd request/fsync latency, leader changes, alarms, disk, and network.
5. Check admission webhooks.
6. Identify large LIST calls, retry storms, controller relists, and watch disconnects.
7. Check CPU, memory, file descriptors, TLS, and load balancer behavior.
8. Use audit data to identify high-volume callers.
9. Fix abusive clients before merely adding API servers.

Do not restart all control-plane components together.

## 88. How do you perform capacity planning?

Include:

- Node capacity and allocatable.
- Requests and limits by node, namespace, and service.
- Historical percentile CPU/memory.
- CPU throttling and memory headroom.
- Ephemeral storage and inode growth.
- Pod density and IP capacity.
- Storage IOPS, throughput, latency, and quota.
- etcd/API growth.
- N+1 and zone-loss capacity.
- rollout surge and drain capacity.
- autoscaler delay and cloud quotas.
- seasonal/business growth.

Report normal and degraded-mode headroom. Use historical telemetry, not snapshots.

# 10. Production Troubleshooting Scenarios

## 89. A node is `NotReady`. What is your method?

```bash
kubectl get node <node> -o wide
kubectl describe node <node>
kubectl get lease <node> -n kube-node-lease -o yaml
```

On the node:

```bash
systemctl status kubelet
journalctl -u kubelet --since '30 min ago'
crictl info
crictl ps -a
df -h
df -i
free -m
ip addr
ip route
```

Check API reachability, TLS, runtime, CNI, cgroups, disk, clock, certificates, and kernel errors. Cordon unstable nodes. Reboot only after evidence collection unless safety requires immediate action.

## 90. A Pod is Pending. How do you troubleshoot it?

```bash
kubectl describe pod <pod> -n <ns>
kubectl get events -n <ns> --sort-by=.metadata.creationTimestamp
kubectl get nodes --show-labels
kubectl get resourcequota,limitrange -n <ns>
kubectl get pvc -n <ns>
```

Classify scheduler constraints: CPU/memory, taints, affinity, quota, unbound PVC, volume topology, extended resources, HostPort, priority, or RuntimeClass. Do not reduce requests without workload evidence.

## 91. A Pod is stuck in `ContainerCreating`. What do you inspect?

```bash
kubectl describe pod <pod>
kubectl get events --field-selector involvedObject.name=<pod>
journalctl -u kubelet --since '20 min ago'
crictl pods
crictl ps -a
```

Look for `FailedCreatePodSandBox`, `FailedMount`, `FailedAttachVolume`, image errors, CNI/IP exhaustion, missing configuration, device plugin, runtime, SELinux, or filesystem problems.

## 92. How do you troubleshoot `ImagePullBackOff`?

```bash
kubectl describe pod <pod>
kubectl get pod <pod> -o jsonpath='{.spec.containers[*].image}'
```

Check image/tag existence, registry credentials, imagePullSecret/service account, DNS/TLS/connectivity, rate limits, proxy/firewall, custom CA trust, CPU architecture, admission policy, and registry outage. Preserve the initial `ErrImagePull` event detail. Do not expose credentials in tickets or shell history.

## 93. A container was OOMKilled. How do you determine why?

```bash
kubectl describe pod <pod>
kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[*].lastState.terminated}'
kubectl top pod <pod> --containers
kubectl describe node <node>
journalctl -k --since '30 min ago' | grep -i -E 'oom|killed process'
```

Distinguish container-limit OOM, node kernel OOM, kubelet eviction, startup spike, leak, page cache/native memory, or sidecar growth. Increasing a limit without capacity analysis can move failure from one Pod to the node.

## 94. Users cannot reach a Service. Give a layer-by-layer runbook.

1. Validate Pods and application logs.
2. Test localhost in the Pod.
3. Test the Pod IP.
4. Check Service selector, port, and targetPort.
5. Inspect EndpointSlices.
6. Test Service DNS and ClusterIP.
7. Check NetworkPolicy.
8. Check service data plane/CNI.
9. Check Ingress/Gateway, load balancer, firewall, DNS, TLS, and health checks.
10. Compare same-node and cross-node traffic.

```bash
kubectl get pod -l app=<app> -o wide
kubectl get svc <svc> -o yaml
kubectl get endpointslice -l kubernetes.io/service-name=<svc> -o yaml
```

## 95. CoreDNS is Running but queries intermittently time out. What do you check?

Check DNS Service reachability, CoreDNS CPU/memory/throttling, upstream latency, UDP drops and TCP fallback, conntrack, CNI loss, NetworkPolicy, node-local cache, `ndots` amplification, MTU, endpoint balance, and node-specific paths.

Test from Pods on different nodes and query individual CoreDNS Pod IPs to separate load balancing from DNS processing.

## 96. A Deployment rollout is stuck. What do you inspect?

```bash
kubectl rollout status deployment/<name>
kubectl describe deployment <name>
kubectl get rs -l app=<label>
kubectl get pods -l app=<label> -o wide
kubectl get events --sort-by=.metadata.creationTimestamp
```

Causes include Pending Pods, image pull, CrashLoopBackOff, failed readiness, insufficient surge capacity, PDB/topology, quota, admission, PVC conflict, or application incompatibility. Pause, fix forward, or roll back only after checking external/schema compatibility.

## 97. An API server is down on one control-plane node. What do you do?

In HA, confirm the load balancer removed the backend and others are healthy. On the node:

```bash
sudo crictl ps -a --name kube-apiserver
sudo crictl logs <container-id>
sudo journalctl -u kubelet --since '30 min ago'
sudo cat /etc/kubernetes/manifests/kube-apiserver.yaml
sudo df -h
sudo timedatectl
```

Check manifest syntax, certificates/SANs, port conflict, etcd connectivity, disk, flags, missing mounts, runtime, firewall, and time. Do not blindly copy another node's manifest or certificate.

## 98. etcd latency is high and API calls time out. How do you respond?

- Freeze unnecessary changes and high-volume automation.
- Check endpoint status, leader stability, disk fsync, CPU, memory, network, and quota.
- Identify heavy API clients using metrics/audit logs.
- Check backups, antivirus, or shared-disk contention.
- Evaluate database size and maintenance need without running expensive defragmentation blindly.
- Preserve quorum and change one member at a time.

Never restart all etcd members together.

## 99. A namespace is stuck in Terminating. How do you investigate?

```bash
kubectl get namespace <ns> -o yaml
kubectl get namespace <ns> -o jsonpath='{.status.conditions}'
kubectl api-resources --verbs=list --namespaced -o name
```

Look for unavailable APIService discovery, unlistable CRDs, remaining objects/finalizers, missing operators, and webhook failures. Restore broken APIs/controllers first. Forced finalization can orphan external or billable resources.

## 100. A validating webhook blocks every deployment. What is the emergency procedure?

1. Confirm the exact webhook and API error.
2. Check Service, EndpointSlices, Pods, DNS, NetworkPolicy, certificate, and logs.
3. Review `failurePolicy`, timeout, selectors, and rules.
4. Restore replicas/certificates if possible.
5. If bypass is required, use break-glass access and narrowly patch selectors/rules or temporary failure behavior with documented risk.
6. Record owner, ticket, expiry, and rollback.
7. Restore enforcement and audit objects created during bypass.

Do not permanently set all webhooks to `Ignore`.

# 11. Enterprise Governance and L3 Design

## 101. How do you design Kubernetes multi-tenancy?

Controls include separate clusters for strong trust boundaries, namespace lifecycle, federated identity and group RBAC, Pod Security Admission, NetworkPolicy, quotas, dedicated service accounts, Secret isolation, node isolation, audit, cost allocation, operator/CRD governance, and restrictions on exec, port-forward, impersonation, and privileged workloads.

A namespace is an organizational and authorization boundary, not the equivalent of a hardened VM boundary.

## 102. How do ResourceQuota and LimitRange differ?

- **ResourceQuota:** Limits aggregate namespace consumption or object counts.
- **LimitRange:** Applies defaults and min/max constraints to individual containers, Pods, or PVCs.

```bash
kubectl get resourcequota,limitrange -n <ns>
kubectl describe resourcequota <name> -n <ns>
```

Default CPU limits can create throttling; object-count quota can block emergency scaling. Governance must be paired with capacity planning.

## 103. What is GitOps, and what risks must an administrator manage?

GitOps stores desired state in version control and uses an automated reconciler.

Benefits: review, audit trail, repeatability, drift detection, promotion, and declarative rollback.

Risks: bad commits fan out quickly, reconciler credentials are privileged, secrets need protection, emergency manual changes may be reverted, CRD ordering matters, pruning can delete critical objects, and Git/reconciler availability becomes part of DR.

Use protected branches, staged rollout, policy checks, health gates, break-glass procedures, and reconciliation suspension runbooks.

## 104. How do you control configuration drift?

- Version-controlled desired state.
- GitOps/configuration reconciliation.
- Standard node images.
- Controlled distribution configuration.
- Admission policy.
- RBAC and operator inventory reviews.
- Audit logs and compliance checks.
- Exceptions with owner and expiry.

Do not blindly overwrite every difference; some drift is emergency remediation, controller-generated state, or evidence of compromise.

## 105. How should cluster-admin access be governed?

Use federated identity, MFA, group-based access, time-bound elevation, separate normal/privileged accounts, tested break-glass credentials, full audit, protected kubeconfigs/CA keys, credential rotation, and alerts on new cluster-admin grants.

Automation should not routinely use cluster-admin.

## 106. How do you design worker-node maintenance?

Include inventory, version compatibility, drain capacity, PDB/topology review, controlled batches, DaemonSet/local-storage handling, storage detach verification, reboot validation, SLO checks, rollback/replacement, and evidence.

```bash
kubectl cordon worker-1
kubectl drain worker-1 --ignore-daemonsets --delete-emptydir-data --timeout=30m
kubectl uncordon worker-1
```

Do not use `--force` or disable eviction protections by habit.

## 107. What platform SLOs would you define?

Examples:

- API availability/latency.
- Scheduling latency.
- DNS success/latency.
- Pod networking availability.
- Volume provision/attach latency.
- Ingress/Gateway availability.
- Node Ready percentage.
- Upgrade success and version age.
- Certificate-expiry margin.
- Backup and restore-test success.
- Critical add-on availability.
- Detection and remediation time.

Tie alerts to error-budget burn and define measurement windows, exclusions, and owners.

## 108. How do you perform a Kubernetes security review?

Review supported versions, API exposure, authentication, RBAC, service accounts, admission, Pod Security labels, privileged workloads, host access, capabilities, seccomp/AppArmor/SELinux, NetworkPolicy, Secret encryption, etcd/backup protection, audit, supply chain, node hardening, kubelet access, webhooks, APIService, CRDs/operators, cloud IAM, public exposure, certificates, and break-glass response.

Prioritize practical exploit paths and business impact.

# 12. Rapid-Fire Command Questions

## 109. What commands do you use for a quick health assessment?

```bash
kubectl cluster-info
kubectl get --raw='/readyz?verbose'
kubectl get nodes -o wide
kubectl get pods -A -o wide
kubectl get events -A --sort-by=.metadata.creationTimestamp
kubectl get apiservices
kubectl top nodes
kubectl get pvc -A
kubectl get volumeattachments
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations
```

Look for unready nodes, failed add-ons, Pending/restarting Pods, APIService failures, webhook dependencies, storage issues, and resource saturation.

## 110. Explain `kubectl auth can-i`.

```bash
kubectl auth can-i get pods -n app
kubectl auth can-i create deployments -n app
kubectl auth can-i get secrets -n app --as=user@example.com
kubectl auth can-i --list -n app \
  --as=system:serviceaccount:app:payments-api
```

It queries authorization. It does not prove authentication, admission, network connectivity, or application success. Impersonation permission is itself sensitive.

## 111. When do you use `kubectl logs --previous`?

Use it after a container restart to obtain logs from the immediately previous instance:

```bash
kubectl logs <pod> -c <container> --previous
```

Useful for CrashLoopBackOff, OOM, startup errors, and liveness restarts. Central logging is required for durable history.

## 112. Explain cordon, drain, and uncordon.

- `cordon`: Stops new scheduling to the node.
- `drain`: Evicts eligible Pods for maintenance.
- `uncordon`: Restores scheduling.

Flags such as `--force`, `--disable-eviction`, `--delete-emptydir-data`, and grace/timeout settings must be used consciously because they can bypass safety or delete local data.

## 113. What evidence should be collected during a Kubernetes incident?

- Start time, scope, user impact, and recent changes.
- Object YAML/status and sorted events.
- Current and previous Pod logs.
- Relevant control-plane/add-on/kubelet/runtime logs.
- Node kernel, disk, memory, network, and clock data.
- Metrics before/during/after.
- Audit and cloud-provider events.
- Commands and changes executed.
- Data-integrity assessment and rollback point.
- Timeline and ownership.

Redact tokens, Secrets, credentials, and personal data.

---

# L3 Interview Answer Framework

For every scenario:

1. **Confirm impact:** cluster, namespace, service, node, region, start time, recent change, and data risk.
2. **Stabilize:** freeze unrelated changes, preserve evidence, cordon, pause rollout, rate-limit, and protect quorum/storage.
3. **Localize the layer.**
4. **Apply the smallest reversible correction.**
5. **Validate Kubernetes health and real business transactions.**
6. **Document root cause and prevention.**

Network path:

```text
Client -> DNS -> Load balancer/Gateway/Ingress -> Service
       -> EndpointSlice -> Pod network -> Container -> Dependency
```

Control-plane path:

```text
Client -> API load balancer -> kube-apiserver
       -> authentication -> authorization -> admission -> etcd
       -> controller/scheduler -> kubelet/runtime/CNI/CSI
```

## Common Weak Answers to Avoid

- Restart the Pod without finding the cause.
- Reboot the node before evidence collection.
- Increase memory without proving the OOM path and headroom.
- Delete finalizers without understanding cleanup.
- Force-detach storage without fencing the old writer.
- Grant cluster-admin to fix RBAC.
- Disable NetworkPolicy or webhooks permanently.
- Restore etcd without encryption keys and external-resource analysis.
- Claim that all Services use iptables.
- Recommend PodSecurityPolicy on modern Kubernetes.
- Say StatefulSet automatically makes a database highly available.
- Treat successful backup creation as proof of recoverability.

---

# Official References

- Kubernetes Documentation: <https://kubernetes.io/docs/>
- Cluster Architecture: <https://kubernetes.io/docs/concepts/architecture/>
- Kubernetes Components: <https://kubernetes.io/docs/concepts/overview/components/>
- Workloads: <https://kubernetes.io/docs/concepts/workloads/>
- Services and Networking: <https://kubernetes.io/docs/concepts/services-networking/>
- NetworkPolicy: <https://kubernetes.io/docs/concepts/services-networking/network-policies/>
- Gateway API: <https://kubernetes.io/docs/concepts/services-networking/gateway/>
- Storage: <https://kubernetes.io/docs/concepts/storage/>
- Scheduling and Eviction: <https://kubernetes.io/docs/concepts/scheduling-eviction/>
- RBAC: <https://kubernetes.io/docs/reference/access-authn-authz/rbac/>
- Pod Security Standards: <https://kubernetes.io/docs/concepts/security/pod-security-standards/>
- Pod Security Admission: <https://kubernetes.io/docs/concepts/security/pod-security-admission/>
- Securing a Cluster: <https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/>
- Operating etcd: <https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/>
- kubeadm Administration: <https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/>
- kubeadm Upgrades: <https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/>
- Version Skew Policy: <https://kubernetes.io/releases/version-skew-policy/>
- Patch Releases: <https://kubernetes.io/releases/patch-releases/>
- API Access Control: <https://kubernetes.io/docs/reference/access-authn-authz/>
- Probes: <https://kubernetes.io/docs/concepts/workloads/pods/probes/>
- Official CVE Feed: <https://kubernetes.io/docs/reference/issues-security/official-cve-feed/>

---

## Version Note

As of **August 1, 2026**, Kubernetes **1.36** is an active supported release series. Always verify the current supported minor and latest patch before installation, upgrade, or interview preparation.

**End of guide**
