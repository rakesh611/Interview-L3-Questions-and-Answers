# OpenShift Advanced Cluster Management (EX432) — Corporate L3 Questions and Detailed Answers

> **Purpose:** Original practice and interview-preparation material for senior OpenShift, platform engineering, SRE, and multicluster operations roles. These are **not leaked or recalled exam questions**.
>
> **Official exam baseline checked on 1 August 2026:** Red Hat OpenShift Container Platform **4.18** and Red Hat Advanced Cluster Management for Kubernetes **2.13**. Red Hat can change objectives; verify the official EX432 page before the exam.

## Audience

- OpenShift administrators with production experience
- SRE, DevOps, platform, cloud, and cluster engineers
- Engineers preparing for DO432/EX432
- OpenShift Virtualization administrators managing VM fleets
- L3 support engineers responsible for governance, observability, GitOps, and cluster lifecycle

## How to use this guide

1. Read the answer, then reproduce the commands in a lab.
2. Replace example names, URLs, storage classes, and credentials with your environment values.
3. Validate every object with `oc explain`, `oc api-resources`, and server-side dry run.
4. Practice troubleshooting from symptoms rather than memorizing commands.
5. Keep all configuration declarative and persistent; the EX432 exam is performance based.

## Exam-objective coverage

- RHACM architecture, installation, agents, console, CLI
- Cluster creation, import, upgrade, removal, and scaling
- ManagedClusterSets, placement, search, RBAC, users, and groups
- Governance policies, PolicySets, Compliance Operator, and troubleshooting
- Observability enablement, management, customization, and troubleshooting
- Kustomize and GitOps application lifecycle
- Importing managed clusters into Argo CD
- OpenShift Virtualization operator and multicluster VM management

## Table of contents

1. Architecture and Installation — Q1–Q10
2. Cluster Lifecycle Management — Q11–Q20
3. Access Control, ClusterSets, Search, and Placement — Q21–Q30
4. Governance and Compliance — Q31–Q43
5. Observability — Q44–Q52
6. Kustomize, GitOps, and Application Lifecycle — Q53–Q63
7. OpenShift Virtualization — Q64–Q69
8. Enterprise Troubleshooting and Design — Q70–Q75
9. Rapid-fire L3 Review — Q76–Q100
10. Command and Resource Cheat Sheet

---
## Q1. Explain RHACM architecture and the responsibility of the hub and managed clusters.

### Detailed answer

RHACM uses a hub-and-agent architecture. The **hub cluster** is an OpenShift cluster where the RHACM operator and the `MultiClusterHub` custom resource are installed. It hosts the central APIs, console integrations, search, placement, governance propagation, application integration, and optional observability services. The hub does not continuously execute arbitrary commands directly against every managed cluster. Instead, it uses asynchronous work and agent-based reconciliation.

A **managed cluster** is registered with the hub. Registration installs the `klusterlet` and related add-ons. The registration agent maintains identity and lease information; the work agent receives `ManifestWork` objects and applies resources locally. Governance controllers evaluate policies on the managed cluster. When observability is enabled, collectors forward selected metrics to the hub-side Thanos stack.

The hub itself normally appears as the `local-cluster` managed cluster. This is convenient but has security and blast-radius implications. Production teams should understand which policies and placements include `local-cluster` and exclude it where a policy must not modify the management plane.

Important architectural objects include:

- `MultiClusterHub`: desired hub configuration.
- `ManagedCluster`: registration and metadata for a cluster.
- `Klusterlet`: managed-cluster agent installation.
- `ManifestWork`: hub-to-managed-cluster work delivery.
- `ManagedClusterAddOn`: feature-specific agent/add-on status.
- `Placement` and `PlacementDecision`: cluster selection.
- `Policy`, `ConfigurationPolicy`, and `PlacementBinding`: governance.
- `MultiClusterObservability`: optional fleet monitoring.


### Commands / manifests

```bash
oc get multiclusterhub -n open-cluster-management
oc get managedclusters
oc get manifestworks -A
oc get managedclusteraddons -A
oc get placements,placementdecisions -A
```


### L3 / production considerations

Treat the hub as a tier-0 management system. Protect its API, etcd, identity provider, object-storage credentials, Git credentials, and backup data. Capacity-plan for fleet size, policy count, search volume, observability cardinality, and application reconciliation load. Separate development and production fleets when governance boundaries or failure domains require it.


### Troubleshooting approach

Start with the registration path: `ManagedCluster` conditions on the hub, klusterlet pods on the managed cluster, add-on conditions, `ManifestWork` status, network/DNS/proxy reachability, and certificate validity. Avoid deleting registration resources until evidence is collected.


---

## Q2. What is the multicluster engine operator, and how does it relate to RHACM?

### Detailed answer

The **multicluster engine for Kubernetes (MCE)** provides the core cluster-lifecycle and multicluster foundation: cluster discovery, registration, provisioning integrations, placement, work distribution, and hosted-control-plane capabilities. RHACM includes and builds on MCE. RHACM adds enterprise governance, observability, application lifecycle integrations, search capabilities, and a broader management console.

This distinction matters operationally. A problem in cluster registration or placement can originate in MCE components even though the administrator experiences it through the RHACM console. Conversely, governance or observability failures are RHACM feature-layer issues rather than basic MCE registration failures.

Do not install overlapping independent MCE and RHACM instances without checking supported topology. Operator ownership, namespaces, CRDs, and component reconciliation can conflict. In an existing environment, inspect installed CSVs, subscriptions, and `MultiClusterEngine`/`MultiClusterHub` resources before changing operators.


### Commands / manifests

```bash
oc get subscription,csv -A | egrep 'advanced-cluster|multicluster-engine'
oc get multiclusterengine -A
oc get multiclusterhub -A
oc get pods -n multicluster-engine
oc get pods -n open-cluster-management
```


### L3 / production considerations

When troubleshooting, classify the failing capability first:

- Registration, provisioning, placement, work delivery: MCE path.
- Governance, policy automation: RHACM governance path.
- Fleet metrics and Grafana: observability path.
- GitOps application placement: RHACM plus OpenShift GitOps path.

This prevents broad, disruptive restarts of unrelated components.


---

## Q3. How do you install RHACM by using Operator Lifecycle Manager?

### Detailed answer

A reliable installation has four stages: verify prerequisites, install the operator, create the hub custom resource, and validate all operands.

1. Confirm the OpenShift version, compute capacity, default or explicit storage, DNS, proxy, and disconnected-registry requirements.
2. Create the operator namespace, typically `open-cluster-management`.
3. Create an `OperatorGroup` scoped as documented for the product.
4. Create a `Subscription` for the RHACM operator and wait for the CSV to reach `Succeeded`.
5. Create `MultiClusterHub`.
6. Wait for the hub resource to report a running/complete phase and validate pods, routes, CRDs, and console integration.

Do not assume that a successful CSV means RHACM is fully ready. The CSV only confirms that the operator installed. The `MultiClusterHub` operator must still deploy and reconcile many operands.


### Commands / manifests

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: open-cluster-management
---
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  name: open-cluster-management
  namespace: open-cluster-management
spec:
  targetNamespaces:
  - open-cluster-management
---
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: advanced-cluster-management
  namespace: open-cluster-management
spec:
  channel: release-2.13
  installPlanApproval: Automatic
  name: advanced-cluster-management
  source: redhat-operators
  sourceNamespace: openshift-marketplace
---
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc apply -f rhacm-install.yaml
oc get sub,csv,installplan -n open-cluster-management
oc get mch -n open-cluster-management -w
oc get pods -n open-cluster-management
```


### L3 / production considerations

Use manual install-plan approval in tightly controlled production environments, but establish a documented upgrade process so subscriptions do not remain indefinitely behind. In disconnected environments, validate mirrored catalogs and all operand images before starting. Back up the hub before upgrades and test the exact upgrade path in a representative lower environment.


### Troubleshooting approach

Check `Subscription` conditions, `InstallPlan`, CSV events, catalog-source health, image-pull errors, operator logs, `MultiClusterHub` conditions, resource quotas, SCC admission, and insufficient CPU/memory. A common error is troubleshooting operands before the operator has a healthy catalog and CSV.


---

## Q4. How do you validate RHACM installation health from the CLI?

### Detailed answer

Validation must cover OLM, the hub custom resource, component deployments, routes, APIs, and functional reconciliation.

- OLM: subscription resolves, install plan completes, CSV is `Succeeded`.
- Hub: `MultiClusterHub` conditions indicate successful reconciliation.
- Workloads: deployments and stateful sets have desired replicas and no restart loops.
- APIs: RHACM CRDs exist and can be listed.
- Console: route and console plug-in are available.
- Registration: `local-cluster` is available and its add-ons report healthy.
- Functional test: create a harmless placement or policy in `inform` mode and confirm status propagation.

Pod status alone is insufficient. A pod may be `Running` while failing readiness, unable to connect to another service, or continuously logging reconciliation errors.


### Commands / manifests

```bash
oc get sub,csv,installplan -n open-cluster-management
oc describe mch multiclusterhub -n open-cluster-management
oc get deploy,sts,pods -n open-cluster-management
oc get managedcluster local-cluster -o yaml
oc get managedclusteraddons -n local-cluster
oc get crd | grep open-cluster-management
oc get route -A | grep -E 'multicloud|management|search'
oc get events -n open-cluster-management --sort-by=.lastTimestamp
```


### L3 / production considerations

Create an operational health checklist and alert on degraded hub components, unavailable managed clusters, add-on failures, policy propagation lag, search ingestion delay, and observability ingestion failures. Record normal pod counts only as a diagnostic baseline, not as a hard-coded health definition, because versions change component composition.


---

## Q5. Explain klusterlet registration, ManifestWork, and managed-cluster add-ons.

### Detailed answer

The **klusterlet** is the agent-side foundation installed in a managed cluster. Its registration agent requests and renews credentials with the hub and updates cluster availability through leases and status. Its work agent watches work assigned by the hub and applies manifests locally.

A `ManifestWork` exists in the managed cluster namespace on the hub. It contains one or more Kubernetes manifests. The work agent applies those resources to the managed cluster and reports conditions such as applied and available. RHACM features use this mechanism indirectly; administrators should understand it because it reveals whether a problem is in placement/propagation or local application.

A `ManagedClusterAddOn` represents a feature-specific extension such as governance, application manager, or observability. Add-ons can be healthy independently of basic registration. A cluster can therefore be `Available=True` while a particular add-on is degraded.


### Commands / manifests

```bash
# Hub
oc get managedcluster <cluster> -o yaml
oc get manifestwork -n <cluster>
oc describe manifestwork <name> -n <cluster>
oc get managedclusteraddon -n <cluster>

# Managed cluster
oc get pods -n open-cluster-management-agent
oc get pods -n open-cluster-management-agent-addon
oc logs -n open-cluster-management-agent deploy/klusterlet-registration-agent
oc logs -n open-cluster-management-agent deploy/klusterlet-work-agent
```


### L3 / production considerations

Use `ManifestWork` status as the contract boundary. If work is never created, investigate placement and hub controllers. If work exists but is not applied, investigate work-agent connectivity and authorization. If applied but unavailable, inspect the created resource on the managed cluster.


---

## Q6. How should a production hub be sized and designed?

### Detailed answer

Hub sizing depends on managed-cluster count, resources per cluster, policy count and evaluation rate, search usage, number of GitOps applications, observability retention, metric cardinality, and user concurrency. A generic node count is not enough.

A production design should include:

- Highly available OpenShift control plane and resilient worker capacity.
- Dedicated worker placement for heavy hub services when justified.
- Storage classes with suitable latency, capacity, expansion, and failure-domain behavior.
- Object storage for observability and backup with independent durability controls.
- Network paths from managed clusters to hub endpoints through proxies/firewalls.
- Identity integration and least-privilege RBAC.
- Backup/restore runbooks and tested recovery objectives.
- Upgrade rings: lab, staging, noncritical production, then critical production.
- Capacity headroom for fleet growth and failure scenarios.

Observability often dominates storage and ingestion requirements. Search can also become expensive if administrators retain excessive resource data or issue broad queries across a large fleet.


### L3 / production considerations

Measure rather than guess. Track API latency, etcd health, CPU throttling, memory working set, pod restarts, PVC latency/capacity, Thanos ingestion/query performance, search indexing delay, policy propagation latency, and number of managed resources. Document saturation thresholds and scaling actions.


---

## Q7. How do you use RHACM from the CLI when the console is unavailable?

### Detailed answer

RHACM is API-driven, so every major operation can be inspected or performed with `oc`. The administrator must know API discovery, namespaces, labels, conditions, and ownership relationships.

Start by discovering resources rather than relying on memory. Use `oc api-resources` and `oc explain`. Then inspect the root object and its dependent resources. For example, policy placement requires `Policy`, `Placement`, `PlacementBinding`, generated replicated policies, and managed-cluster controller status.

CLI work should be declarative. Export or maintain YAML in Git, use `oc diff`, validate with server-side dry run, and avoid ad hoc patching that is not recorded.


### Commands / manifests

```bash
oc api-resources | grep -i open-cluster
oc api-resources | grep -Ei 'managedcluster|placement|policy|manifestwork'
oc explain managedcluster.spec
oc explain placement.spec --recursive
oc get managedclusters --show-labels
oc get policies -A
oc get placements,placementdecisions -A
oc get manifestworks -A
oc get managedclusteraddons -A
oc auth can-i --list
```


### L3 / production considerations

During an incident, capture `oc get ... -o yaml`, events, and logs before editing resources. Use `--context` or explicit `KUBECONFIG` to avoid running hub commands against a managed cluster or vice versa.


---

## Q8. What are the key network and certificate requirements between a hub and managed cluster?

### Detailed answer

The managed cluster must resolve and reach the hub registration and work endpoints, usually over HTTPS. The hub must expose valid routes/endpoints whose certificates are trusted by the agent. Proxies, custom CAs, egress controls, NAT, and load balancers must preserve this path.

Registration bootstrap normally uses a token or generated import manifests. After approval, the managed cluster receives client credentials and rotates them. Time synchronization is critical because certificate validity and token checks are time sensitive.

For observability, additional metric-upload endpoints and mutual TLS are involved. A cluster may register correctly but fail to send metrics because only registration endpoints were allowed through the firewall.


### Commands / manifests

```bash
# Managed cluster
oc get proxy cluster -o yaml
oc get infrastructure cluster -o yaml
oc get route -A | head
curl -vk https://<hub-api-or-route>/
openssl s_client -connect <host>:443 -servername <host> </dev/null

# Agent evidence
oc logs -n open-cluster-management-agent deploy/klusterlet-registration-agent
oc get csr
```


### L3 / production considerations

Maintain a documented endpoint matrix for registration, work, observability, Git, identity, object storage, and external providers. Certificate renewal and custom-CA rotation must be tested. Avoid bypassing TLS verification as a permanent fix.


---

## Q9. How do you safely upgrade RHACM?

### Detailed answer

A safe upgrade begins by confirming the supported OpenShift/RHACM version matrix and permitted upgrade path. Back up the hub, validate object storage and recovery procedures, review release notes for deprecations, and test in a representative environment.

Operational sequence:

1. Confirm current CSV, subscription channel, `MultiClusterHub`, and MCE status.
2. Resolve degraded components and failed managed-cluster add-ons before upgrade.
3. Take and verify a hub backup.
4. Change the subscription channel or approve the install plan according to change policy.
5. Monitor CSV replacement, operator logs, CRD updates, and hub conditions.
6. Validate registration, policies, search, observability, GitOps, and virtualization after upgrade.
7. Keep a tested recovery plan; OLM downgrades are generally not a casual rollback mechanism.

Do not treat operator rollback like a package-manager downgrade. CRD schemas and persisted objects may have been migrated.


### Commands / manifests

```bash
oc get sub advanced-cluster-management -n open-cluster-management -o yaml
oc get csv -n open-cluster-management
oc get mch -n open-cluster-management -o yaml
oc get mce -A -o yaml
oc get installplan -n open-cluster-management
oc get events -A --sort-by=.lastTimestamp | tail -100
```


### L3 / production considerations

Define acceptance tests before the change: a cluster remains available, a sample policy evaluates, a placement returns decisions, a GitOps app syncs, a Grafana query returns recent data, and a representative VM is visible. These tests are more meaningful than “all pods running.”


---

## Q10. How do you uninstall RHACM without leaving managed clusters in an unsafe state?

### Detailed answer

Uninstallation is a lifecycle project, not a single `oc delete subscription` command. Decide whether managed clusters will be detached, moved to a replacement hub, or continue unmanaged. Export required policies, GitOps definitions, credentials references, and operational data first.

A safe sequence is:

1. Stop or freeze configuration changes.
2. Back up the hub and Git repositories.
3. Disable or remove policies that could continue enforcing undesired state.
4. Detach or migrate managed clusters according to the supported process.
5. Remove optional components such as observability only after data-retention decisions.
6. Delete `MultiClusterHub` and wait for operand cleanup.
7. Remove the subscription, CSV/operator resources, and only then review remaining CRDs/finalizers.

Never force-delete namespaces or remove finalizers at the beginning. That can orphan cluster registrations, webhooks, credentials, and cluster-scoped resources.


### Commands / manifests

```bash
oc get managedclusters
oc get policies -A
oc get applications,subscriptions,channels -A
oc get mch -n open-cluster-management
oc delete mch multiclusterhub -n open-cluster-management
oc get all -n open-cluster-management
```


### L3 / production considerations

For critical fleets, migrate clusters in waves and verify governance and GitOps ownership on the new hub before removing the old hub. Prevent two hubs from enforcing conflicting desired state against the same cluster.


---

## Q11. Describe the supported methods to import an existing OpenShift cluster.

### Detailed answer

An existing cluster can be imported through the console, generated import manifests, CLI automation, or supported auto-import mechanisms. The essential workflow is the same:

1. Create a `ManagedCluster` object on the hub.
2. Obtain the generated import resources or create the supported auto-import secret.
3. Apply the import resources to the target cluster.
4. Approve/accept registration if required.
5. Validate `ManagedCluster` availability, leases, klusterlet pods, and add-ons.

The cluster name becomes a key identity and namespace on the hub. Choose a stable, unique naming standard that encodes environment or location without exposing sensitive data.


### Commands / manifests

```bash
# Hub
oc apply -f - <<'EOF'
apiVersion: cluster.open-cluster-management.io/v1
kind: ManagedCluster
metadata:
  name: prod-eu-01
  labels:
    environment: prod
    region: eu-west
spec:
  hubAcceptsClient: true
EOF

oc get secret -n prod-eu-01 prod-eu-01-import -o jsonpath='{.data.import\.yaml}' | base64 -d > import.yaml
oc get secret -n prod-eu-01 prod-eu-01-import -o jsonpath='{.data.crds\.yaml}' | base64 -d > crds.yaml

# Target cluster
oc apply -f crds.yaml
oc apply -f import.yaml
```


### L3 / production considerations

Store generated import material securely because it bootstraps trust. Confirm that the logged-in `oc` context is the target cluster before applying. In automated factories, use short-lived service-account tokens and secret-management controls rather than human kubeadmin credentials.


### Troubleshooting approach

If the cluster remains pending, inspect `ManagedCluster` conditions, CSRs, klusterlet namespace pods, hub endpoint reachability, proxy configuration, clock skew, and import-token expiry. If registration succeeds but add-ons fail, troubleshoot each `ManagedClusterAddOn` separately.


---

## Q12. What is the difference between detach and destroy?

### Detailed answer

**Detach** removes the cluster from RHACM management while leaving the underlying cluster running. **Destroy** deprovisions a cluster that RHACM or an integrated provisioning mechanism owns. The distinction is critical because destroying can remove infrastructure, control planes, machines, and associated cloud resources.

For imported clusters, the normal action is detach. For provisioned clusters, determine whether the cluster was created by Hive, a hosted-control-plane workflow, a cluster pool, or another provider integration. Confirm ownership and retention requirements for persistent data, DNS, load balancers, object storage, and cloud credentials.

Before either action, check whether GitOps, governance, backup, DR, or centralized identity relies on the cluster. Remove or re-home application placements first so a GitOps controller does not immediately recreate workloads elsewhere unexpectedly.


### Commands / manifests

```bash
oc get managedcluster <name> -o yaml
oc get clusterdeployment -A | grep <name>
oc get hostedcluster -A | grep <name>
oc get manifestwork -n <name>
oc get placementdecision -A -o yaml | grep -B3 -A3 <name>
```


### L3 / production considerations

Use a formal decommission checklist with business owner approval. Capture final backup evidence, revoke credentials, remove DNS and firewall rules, delete cloud resources in dependency order, and verify that costs stop. A disappearing `ManagedCluster` object does not prove infrastructure was cleaned up.


---

## Q13. How do you label clusters for reliable placement and governance?

### Detailed answer

Cluster labels are an API contract. Use stable business and technical dimensions such as `environment`, `region`, `platform`, `data-classification`, `team`, `capacity-tier`, or `compliance-profile`. Avoid labels based on temporary conditions unless a controller owns and updates them.

A good label taxonomy has documented keys and allowed values, ownership, and change control. Placements and policies should select labels that represent intent. For example, a PCI policy should select `compliance-profile=payments`, not a list of individual cluster names.

Do not overload a single label with multiple meanings. `environment=prod` does not imply geography, data classification, or application ownership.


### Commands / manifests

```bash
oc label managedcluster prod-eu-01 environment=prod region=eu-west --overwrite
oc label managedcluster prod-eu-01 compliance-profile=payments --overwrite
oc get managedclusters -l environment=prod --show-labels
```


### L3 / production considerations

Protect critical labels with RBAC and policy. A user who can relabel clusters may indirectly change where applications or enforcement policies run. Audit label changes and use admission controls for restricted taxonomy keys.


---

## Q14. How do you scale a cluster managed through RHACM?

### Detailed answer

The scaling method depends on how the cluster was created and its infrastructure provider. For a Hive-provisioned cluster, machine pools or provider-specific lifecycle resources may be managed from the hub. For an imported cluster, RHACM can provide visibility, but scaling may still be performed through the managed cluster's Machine API, autoscalers, or infrastructure automation.

First identify ownership:

- Hive `ClusterDeployment` and `MachinePool`.
- Hosted control plane `HostedCluster` and `NodePool`.
- Imported OpenShift cluster using `MachineSet`/`MachineAutoscaler`.
- Bare-metal or UPI cluster requiring external provisioning.

Scaling control-plane nodes is different from worker scaling and may have strict topology requirements. Always verify provider quotas, subnet capacity, load-balancer limits, ignition/bootstrap reachability, and storage topology.


### Commands / manifests

```bash
# Hub discovery
oc get clusterdeployment,machinepool -A
oc get hostedcluster,nodepool -A

# Managed cluster
oc get machinesets -n openshift-machine-api
oc get machineautoscalers -n openshift-machine-api
oc scale machineset/<name> -n openshift-machine-api --replicas=5
oc get machines,nodes -o wide
```


### L3 / production considerations

Scale based on workload signals and failure scenarios, not average CPU alone. Verify PodDisruptionBudgets, topology spread, storage attach limits, ingress capacity, and license impact. After scaling, confirm nodes reach `Ready`, MachineConfigPools converge, and workloads rebalance as intended.


---

## Q15. How do you perform a managed-cluster upgrade from an enterprise perspective?

### Detailed answer

An enterprise upgrade is a staged change across cluster rings. RHACM can centralize inventory and initiate supported upgrade workflows, but the underlying OpenShift Cluster Version Operator still enforces version rules on each managed cluster.

Preparation includes:

- Verify current version, target channel, conditional updates, and operator compatibility.
- Resolve degraded cluster operators and MachineConfigPools.
- Check platform capacity to tolerate node drains.
- Validate backups and rollback/recovery options.
- Pause risky workload changes.
- Upgrade canary clusters first, then progressively broader rings.

After initiation, monitor the managed cluster's `ClusterVersion`, cluster operators, nodes, MachineConfigPools, workload health, and business SLOs. A fleet dashboard status is a summary, not a substitute for cluster-level evidence.


### Commands / manifests

```bash
# On managed cluster
oc get clusterversion
oc adm upgrade
oc get clusteroperators
oc get mcp
oc get nodes
oc get events -A --sort-by=.lastTimestamp | tail -100
```


### L3 / production considerations

Define stop conditions: API error rate, ingress failure, critical operator degradation, workload SLO breach, storage errors, or node drain blockage. Never push the next ring merely because the previous cluster reports `Completed`; require an observation period and application validation.


---

## Q16. How do cluster pools improve provisioning at scale?

### Detailed answer

A cluster pool maintains a set of pre-created or pre-provisioned clusters that can be claimed when needed. This reduces lead time for development, test, training, or ephemeral environments. A `ClusterClaim` binds a consumer to a cluster from the pool.

Pools improve speed but introduce capacity and security questions: who can claim clusters, how long claims live, how clusters are sanitized and returned, how cloud cost is controlled, and what baseline configuration is applied after claim.

Do not treat a returned cluster as automatically clean. Validate the supported hibernation/reuse model, remove tenant credentials and workloads, rotate secrets, and reapply baseline governance before reissue.


### Commands / manifests

```bash
oc api-resources | grep -Ei 'clusterpool|clusterclaim'
oc get clusterpool -A
oc get clusterclaim -A
oc describe clusterpool <pool> -n <namespace>
```


### L3 / production considerations

Use quotas and approval workflows. Tag cloud resources for chargeback, enforce maximum pool size, and alert on unclaimed or stuck clusters. Maintain separate pools for security zones rather than mixing trust levels.


---

## Q17. How do you troubleshoot a managed cluster that shows Unknown or Unavailable?

### Detailed answer

Start from the `ManagedCluster` conditions. Determine whether the issue is loss of lease, registration failure, work-agent failure, or complete network partition.

A disciplined path is:

1. Check `ManagedCluster` conditions and last transition times.
2. Check lease objects in the managed-cluster namespace on the hub.
3. Check registration and work agent pods/logs on the managed cluster.
4. Test DNS, route, proxy, firewall, and TLS from the managed cluster.
5. Check certificates and CSRs.
6. Confirm the cluster API and nodes are themselves healthy.
7. Inspect hub registration/work controllers if many clusters fail simultaneously.

If only one cluster fails, suspect local networking, certificates, proxy, or agent state. If many clusters fail at the same time, prioritize hub endpoint, load balancer, certificate, DNS, or hub capacity.


### Commands / manifests

```bash
# Hub
oc describe managedcluster <cluster>
oc get lease -n <cluster>
oc get managedclusteraddon -n <cluster>
oc get events -n <cluster> --sort-by=.lastTimestamp

# Managed cluster
oc get pods -n open-cluster-management-agent
oc logs -n open-cluster-management-agent deploy/klusterlet-registration-agent --since=30m
oc logs -n open-cluster-management-agent deploy/klusterlet-work-agent --since=30m
oc get csr
```


### L3 / production considerations

Do not immediately re-import. Re-importing can destroy useful evidence and may create duplicate or conflicting resources. Capture logs, YAML, timestamps, and network tests first.


---

## Q18. How do you recover from a cluster name or identity mismatch during import?

### Detailed answer

RHACM associates the managed cluster identity with the `ManagedCluster` name, its namespace on the hub, and registration credentials. Reusing a name for a different physical cluster or applying old import manifests can produce certificate and identity confusion.

Confirm the target cluster's existing klusterlet resources and the hub-side `ManagedCluster`. Determine whether this is the same cluster being reconnected or a different cluster incorrectly reusing the name. If it is a different cluster, use a new unique name. If it is a legitimate re-import, follow the supported detach/reimport process and remove stale credentials only after backup and evidence collection.

Never copy the `open-cluster-management-agent` namespace from one cluster to another as a registration method.


### Commands / manifests

```bash
# Hub
oc get managedcluster <name> -o yaml
oc get secret -n <name>
oc get csr | grep <name>

# Target
oc get namespace open-cluster-management-agent
oc get klusterlet -A -o yaml
oc get secret -n open-cluster-management-agent
```


### L3 / production considerations

Include immutable platform identifiers in your CMDB and labels. Human-friendly cluster names alone are not enough for lifecycle automation.


---

## Q19. How do you manage disconnected or proxy-based managed clusters?

### Detailed answer

Disconnected management requires two independent designs: image/content availability and network reachability to the hub or intermediary endpoints.

- Mirror RHACM, MCE, OpenShift GitOps, Compliance Operator, OpenShift Virtualization, and all workload images into an approved registry.
- Configure `ImageDigestMirrorSet`/related mirror resources as appropriate for the OpenShift version.
- Configure cluster-wide proxy and trusted CA bundles.
- Allow the specific hub registration, work, and observability endpoints.
- Ensure Git repositories and object storage are available through approved paths.
- Validate that catalogs resolve to mirrored images and do not reference the public registry unexpectedly.

A cluster can register while later add-ons fail to pull images. Therefore validate both connectivity and image resolution for every enabled feature.


### Commands / manifests

```bash
oc get proxy cluster -o yaml
oc get configmap trusted-ca-bundle -n openshift-config -o yaml
oc get imagedigestmirrorset
oc get catalogsource -n openshift-marketplace
oc get pods -A | grep -E 'ImagePull|ErrImage'
```


### L3 / production considerations

Version-control mirror manifests and run a preflight that pulls every required digest. Treat registry garbage collection and certificate rotation as production changes because they can break future reconciliation even when existing pods remain running.


---

## Q20. What evidence proves that a cluster import is complete and operational?

### Detailed answer

Completion requires more than seeing the cluster in the console. Evidence should include:

- `ManagedCluster` accepted and `Available=True`.
- Lease renewals are current.
- Klusterlet registration/work agents are ready.
- Required `ManagedClusterAddOn` conditions are available.
- A small `ManifestWork` or policy reaches applied/compliant state.
- Search returns resources from the cluster if search is enabled.
- Observability metrics are recent if observability is enabled.
- GitOps placement can select the cluster if that is part of the design.

This is an end-to-end test of identity, connectivity, work delivery, local apply, and status return.


### Commands / manifests

```bash
oc get managedcluster <cluster> -o jsonpath='{range .status.conditions[*]}{.type}{"="}{.status}{" "}{.reason}{"\n"}{end}'
oc get lease -n <cluster>
oc get managedclusteraddon -n <cluster>
oc get manifestwork -n <cluster>
oc get policies -A -o wide
```


---

## Q21. Explain ManagedClusterSet and ManagedClusterSetBinding.

### Detailed answer

A `ManagedClusterSet` groups managed clusters into an administrative boundary. A cluster is associated with a set through the `cluster.open-cluster-management.io/clusterset` label. The set can then be granted to namespaces through a `ManagedClusterSetBinding`.

This provides a scalable separation model: a platform team owns fleet membership; application or tenant namespaces receive access only to approved cluster sets. Placement resources in a namespace can select clusters only from bound sets, subject to RBAC.

ClusterSets are not merely tags. Changing membership can change which teams can deploy applications or policies to a cluster.


### Commands / manifests

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production-eu
---
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSetBinding
metadata:
  name: production-eu
  namespace: payments-gitops
spec:
  clusterSet: production-eu
```

```bash
oc label managedcluster prod-eu-01 cluster.open-cluster-management.io/clusterset=production-eu --overwrite
oc get managedclustersets
oc get managedclustersetbindings -A
```


### L3 / production considerations

Separate cluster-set membership administration from application deployment permissions. Audit membership changes because they can silently widen a tenant's deployment reach.


---

## Q22. How does Placement select clusters?

### Detailed answer

The `Placement` API evaluates eligible clusters based on bound cluster sets, label selectors, predicates, tolerations, prioritizers, and optional decision count. A placement controller creates one or more `PlacementDecision` resources containing the selected cluster names.

Consumers do not need to duplicate selection logic. Governance, GitOps integration, add-on configuration, and other controllers can reference the same placement. This enables a declarative separation between “what should run” and “where it should run.”

A placement result is dynamic. Label, availability, taint, and scoring changes can alter decisions. Applications must be designed for movement or use explicit controls to prevent unintended relocation.


### Commands / manifests

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: prod-eu-placement
  namespace: payments-gitops
spec:
  numberOfClusters: 2
  predicates:
  - requiredClusterSelector:
      labelSelector:
        matchLabels:
          environment: prod
          region: eu-west
```

```bash
oc get placement -A
oc get placementdecision -A -o yaml
```


### L3 / production considerations

Treat placement as code. Test selection with nonproduction labels first. Alert when a placement returns zero decisions or changes unexpectedly. For stateful workloads, combine placement with data locality and DR orchestration rather than assuming stateless movement.


---

## Q23. What is the difference between legacy PlacementRule and Placement?

### Detailed answer

`PlacementRule` is the older application-placement API used by earlier RHACM workflows. The newer `Placement` API is part of the open-cluster-management cluster API and offers a more extensible model with cluster-set binding, predicates, prioritizers, tolerations, and `PlacementDecision` resources.

For new design, prefer `Placement` where supported. During migration, identify all consumers because governance bindings, subscriptions, or GitOps integrations may reference the old object. Do not delete a `PlacementRule` merely after creating a similarly named `Placement`; update each reference and verify decisions.


### Commands / manifests

```bash
oc get placementrules -A
oc get placements -A
oc get placementdecisions -A
oc get placementbindings -A -o yaml
oc get gitopsclusters -A -o yaml
```


### L3 / production considerations

Build migration tests that compare old and new selected-cluster sets before cutover. Differences in availability handling or label logic can produce unexpected workload movement.


---

## Q24. How do taints and tolerations affect placement?

### Detailed answer

Managed-cluster taints communicate conditions or administrative intent that should exclude a cluster from placement unless the placement explicitly tolerates them. Common system taints represent unavailable or unreachable clusters. Custom taints can reserve clusters for specific workloads or mark maintenance state.

A toleration does not force selection; it only permits a tainted cluster to remain eligible. Label predicates and other rules still apply.

Use taints carefully because broad tolerations can cause applications or policies to target clusters that are unreachable. Governance sometimes intentionally tolerates unavailable clusters so that desired policy objects remain associated and reapply when connectivity returns.


### Commands / manifests

```bash
oc get managedcluster <cluster> -o jsonpath='{.spec.taints}' | jq
oc get placement <name> -n <ns> -o yaml
oc get placementdecision -n <ns> -l cluster.open-cluster-management.io/placement=<name> -o yaml
```


### L3 / production considerations

Document who owns custom taints and when they are removed. Avoid manual emergency taints that are never cleared; automate expiry or track them through incident/change records.


---

## Q25. How do you design RBAC for multiple teams and cluster sets?

### Detailed answer

Use a layered RBAC model:

1. **Platform administrators** manage RHACM installation, cluster registration, cluster-set membership, and global governance.
2. **Fleet administrators** manage a defined set of clusters.
3. **Application teams** receive namespaced permissions to create placements, GitOps resources, or policies only within approved namespaces and bound cluster sets.
4. **Auditors** receive read-only access to policies, compliance, search, and selected cluster metadata.

Use groups rather than individual user bindings. Keep cluster-scoped permissions rare. A namespaced `RoleBinding` cannot grant access to a cluster set unless the required cluster-set binding and relevant cluster roles are also present.

Test access as the actual user with `oc auth can-i`. Console visibility can differ from mutation authority; verify both.


### Commands / manifests

```bash
oc adm policy add-role-to-group admin payments-platform -n payments-gitops
oc auth can-i create placements.cluster.open-cluster-management.io \
  -n payments-gitops --as=user@example.com
oc auth can-i get managedclusters --as=user@example.com
oc auth can-i --list -n payments-gitops --as=user@example.com
oc get rolebindings,clusterrolebindings -A | grep payments-platform
```


### L3 / production considerations

Prevent privilege escalation through writable Git repositories, policy templates, operator subscriptions, or service-account token secrets. RBAC review must include what a user can cause controllers to do, not only direct Kubernetes verbs.


---

## Q26. Why can a user see a cluster but not deploy to it?

### Detailed answer

Read visibility and deployment authority are separate. The user may have permission to list managed clusters or use search but lack one of the following:

- Permission to create the application/policy resources in the namespace.
- A `ManagedClusterSetBinding` for the target set in that namespace.
- Permission to use or reference the placement.
- Argo CD project or destination permission.
- Git repository write access.
- Permissions for the controller service account to create destination resources.

Troubleshoot each authorization boundary rather than granting cluster-admin.


### Commands / manifests

```bash
oc auth can-i create placements -n <ns> --as=<user>
oc auth can-i create applications.argoproj.io -n openshift-gitops --as=<user>
oc get managedclustersetbinding -n <ns>
oc get placementdecision -n <ns>
oc get appproject -n openshift-gitops -o yaml
```


### L3 / production considerations

Use impersonation in a safe read-only test and inspect controller events. A UI button being disabled is useful evidence but not the root cause.


---

## Q27. How does the RHACM search engine help L3 administrators?

### Detailed answer

Search aggregates selected Kubernetes resource metadata from managed clusters and provides fleet-wide query capability. It is useful for locating workloads, versions, labels, owners, VM resources, policy-related objects, and operational drift without switching kubeconfigs repeatedly.

L3 use cases include:

- Find all pods using a vulnerable image digest.
- Identify clusters running a deprecated API resource.
- Find VMs in a stopped or error state.
- Locate workloads missing an ownership label.
- Determine where a ConfigMap or Operator subscription exists.

Search data is eventually consistent. It should not be treated as the sole source of truth for a destructive action. Validate on the target cluster before remediation.


### Commands / manifests

```bash
# Resource inventory useful alongside console search
oc get managedclusters --show-labels
oc get manifestworks -A
oc get searchcustomizations -A 2>/dev/null || true
```


### L3 / production considerations

Control access because search can expose metadata across clusters. Monitor ingestion lag and storage growth. Build saved operational queries for recurring incident patterns, but confirm critical state directly through the managed-cluster API.


---

## Q28. How do you secure cluster-label changes?

### Detailed answer

Cluster labels influence placement, governance, and tenant access. Therefore `patch` or `update` on `ManagedCluster` is a high-impact permission. Restrict it to fleet administrators or narrowly scoped automation.

Use admission policies to protect keys such as `environment`, `data-classification`, `compliance-profile`, and cluster-set membership. Audit all changes and reconcile required labels from an authoritative source such as Git or CMDB automation.

A malicious or accidental label change can remove a production security policy or cause an application to deploy into the wrong jurisdiction.


### Commands / manifests

```bash
oc auth can-i patch managedclusters --as=<user>
oc get managedcluster <name> --show-labels
oc get events -A --field-selector involvedObject.kind=ManagedCluster
```


### L3 / production considerations

Design label governance like network or IAM governance. Include peer review, ownership, allowed-value validation, and drift reconciliation.


---

## Q29. How do you grant a group access to policies without granting full cluster administration?

### Detailed answer

Create a dedicated namespace for the team's policies, bind the permitted cluster set into that namespace, and grant the group a role that can manage `Policy`, `Placement`, `PlacementBinding`, and related namespaced resources. Keep cluster-set membership and global operator management with platform administrators.

Use `inform` as the default policy remediation action for delegated authors. Promotion to `enforce` should follow review because a configuration policy can create or modify powerful cluster resources.

Also restrict which Kubernetes kinds may be embedded in policy templates. Native RBAC on `Policy` creation does not fully express the risk of the resources a privileged policy controller can enforce.


### Commands / manifests

```bash
oc new-project payments-policy
oc adm policy add-role-to-group edit payments-policy-authors -n payments-policy
oc get managedclustersetbinding -n payments-policy
oc auth can-i create policies.policy.open-cluster-management.io \
  -n payments-policy --as=<user>
```


### L3 / production considerations

Use Git-based review and Policy Generator for production. Separate authors, approvers, and deployment automation. Never use broad `edit` blindly if it permits secrets or role bindings beyond the intended policy workflow.


---

## Q30. How do you troubleshoot a Placement that returns no decisions?

### Detailed answer

Check the entire eligibility chain:

1. The `Placement` is in the expected namespace.
2. At least one `ManagedClusterSetBinding` exists in that namespace.
3. Candidate clusters belong to the bound set.
4. Labels satisfy every predicate.
5. Taints are tolerated.
6. Cluster claims or selectors use valid keys and values.
7. `numberOfClusters` and prioritizers are valid.
8. Placement controller is healthy.

A common mistake is creating a placement in a tenant namespace without binding the cluster set there. Another is changing a label key while leaving old selectors in Git.


### Commands / manifests

```bash
oc get placement <name> -n <ns> -o yaml
oc describe placement <name> -n <ns>
oc get managedclustersetbinding -n <ns>
oc get managedclusters --show-labels
oc get managedcluster -o custom-columns=NAME:.metadata.name,SET:.metadata.labels.cluster\.open-cluster-management\.io/clusterset,TAINTS:.spec.taints
oc get placementdecision -n <ns> -l cluster.open-cluster-management.io/placement=<name> -o yaml
```


### L3 / production considerations

Avoid “fixing” the issue by removing all selectors. That can widen deployment to unintended clusters. Compare desired candidate set with actual decisions and make the smallest controlled correction.


---

## Q31. Explain RHACM governance architecture.

### Detailed answer

Governance has hub-side and managed-cluster components.

On the hub:

- A root `Policy` contains one or more policy templates.
- `Placement` selects target clusters.
- `PlacementBinding` connects the policy or `PolicySet` to the placement.
- The policy propagator creates replicated policy objects in managed-cluster namespaces and aggregates compliance.
- Add-on management ensures policy controllers are installed on managed clusters.

On each managed cluster:

- Configuration policy controller evaluates Kubernetes resources.
- Certificate policy controller evaluates certificate conditions.
- Operator policy controller manages or reports operator compliance where supported.
- Template synchronization and related controllers evaluate and optionally remediate desired state.

Compliance flows back from managed-cluster controllers to replicated policies and then to the root policy status.


### Commands / manifests

```bash
oc get policies -A
oc get placements,placementbindings -A
oc get policies -n <managed-cluster-name>
oc get managedclusteraddon governance-policy-framework -n <managed-cluster-name>
oc get pods -n open-cluster-management-agent-addon
```


### L3 / production considerations

A root policy being `NonCompliant` is an aggregate. Drill down by cluster, then policy template, then the exact object mismatch. Do not start by restarting all governance pods.


---

## Q32. Create and explain a basic ConfigurationPolicy.

### Detailed answer

A `ConfigurationPolicy` declares how a Kubernetes object should exist on selected managed clusters. It is embedded under `spec.policy-templates` of a root RHACM `Policy`.

Key fields:

- `remediationAction`: `inform` reports drift; `enforce` attempts correction.
- `severity`: governance classification for reporting.
- `object-templates`: list of desired objects.
- `complianceType`: commonly `musthave`, `mustnothave`, or `mustonlyhave`.

`musthave` checks that required fields exist but can tolerate additional fields. `mustonlyhave` is stricter and can cause unintended changes when controllers add defaults; use it carefully.


### Commands / manifests

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: Policy
metadata:
  name: require-team-label
  namespace: platform-policies
spec:
  disabled: false
  remediationAction: inform
  policy-templates:
  - objectDefinition:
      apiVersion: policy.open-cluster-management.io/v1
      kind: ConfigurationPolicy
      metadata:
        name: require-team-label
      spec:
        remediationAction: inform
        severity: medium
        object-templates:
        - complianceType: musthave
          objectDefinition:
            apiVersion: v1
            kind: Namespace
            metadata:
              name: payments
              labels:
                owner: payments-team
```


### L3 / production considerations

Begin in `inform`, observe impact, then promote to `enforce` through Git review. Avoid enforcing controller-managed status fields or autogenerated metadata.


---

## Q33. What roles do PlacementBinding and PolicySet play?

### Detailed answer

A `PlacementBinding` links subjects—one or more `Policy` or `PolicySet` objects—to a placement. Without the binding, the policy exists but is not propagated.

A `PolicySet` groups related policies, such as a CIS baseline, logging baseline, or production platform baseline. The set can be bound once rather than creating identical bindings for every policy. PolicySet status provides aggregate compliance but should not replace investigation of individual policy violations.

Keep policy grouping aligned with operational ownership and rollout. A huge monolithic set makes exception handling and staged enforcement difficult.


### Commands / manifests

```yaml
apiVersion: policy.open-cluster-management.io/v1beta1
kind: PolicySet
metadata:
  name: production-baseline
  namespace: platform-policies
spec:
  policies:
  - require-team-label
  - require-network-policy
---
apiVersion: policy.open-cluster-management.io/v1
kind: PlacementBinding
metadata:
  name: production-baseline-binding
  namespace: platform-policies
placementRef:
  apiGroup: cluster.open-cluster-management.io
  kind: Placement
  name: production-placement
subjects:
- apiGroup: policy.open-cluster-management.io
  kind: PolicySet
  name: production-baseline
```


### L3 / production considerations

Use multiple sets for mandatory controls, recommended controls, and environment-specific controls. This supports separate exception and enforcement processes.


---

## Q34. Explain inform versus enforce remediation and the risk of enforcement.

### Detailed answer

`inform` evaluates and reports compliance without changing the managed cluster. `enforce` asks the policy controller to create, update, or remove resources to satisfy the policy when that controller and policy type support remediation.

Enforcement is not automatically safe. Risks include:

- Reverting emergency changes before the incident is understood.
- Fighting with another GitOps or operator controller.
- Replacing fields that are defaulted or mutated by admission.
- Removing resources when `mustnothave` is used incorrectly.
- Applying a policy to `local-cluster` unintentionally.
- Breaking workloads through RBAC, SCC, network, or operator changes.

Use an enforcement lifecycle: author → dry review → inform → impact analysis → canary enforce → broader rollout → continuous monitoring.


### Commands / manifests

```bash
oc patch policy <name> -n <ns> --type=merge \
  -p '{"spec":{"remediationAction":"enforce"}}'
oc get policy <name> -n <ns> -o yaml
```


### L3 / production considerations

Define an emergency pause mechanism, such as changing the root policy to `inform` in Git and suspending automation. Document controller ownership so two reconcilers do not continuously overwrite each other.


---

## Q35. How do policy templates use hub-side lookup functions safely?

### Detailed answer

RHACM policy templates can resolve values from hub resources and, in supported contexts, managed-cluster resources. Functions such as `fromSecret`, `fromConfigMap`, `lookup`, and protection helpers enable per-cluster configuration without duplicating every policy.

Secrets must be handled carefully. The templating system can protect values when propagating them, but the hub still becomes a sensitive source. Limit access to policy YAML, generated resources, logs, and backup data. Never place plaintext secrets directly in Git.

Template resolution can fail because of missing namespace, wrong cluster scope, RBAC, malformed function syntax, or a value not existing when the policy is rendered.


### Commands / manifests

```yaml
# Conceptual example inside an objectDefinition
stringData:
  endpoint: '{{hub fromConfigMap "platform-policies" "cluster-endpoints" .ManagedClusterName hub}}'
  token: '{{hub fromSecret "platform-policies" "api-token" "token" | protect hub}}'
```


### L3 / production considerations

Prefer external secret-management integration and short-lived credentials. Template only the minimal secret material required. Test missing-value behavior and rotation before production rollout.


### Troubleshooting approach

Inspect root policy events, replicated policy annotations/status, governance controller logs, and existence/RBAC of referenced hub objects. Do not print decoded secrets during troubleshooting.


---

## Q36. What is an OperatorPolicy and when should it be used?

### Detailed answer

An `OperatorPolicy` expresses desired state for an operator managed through OLM, including catalog source expectations, subscription configuration, install-plan behavior, and operator health. It reduces the need to model every OLM object manually with generic configuration policies.

Use it when you need consistent operator installation and compliance across clusters. Examples include Compliance Operator, OpenShift GitOps, logging, or OpenShift Virtualization, subject to version support.

Operator installation has supply-chain impact. Pin approved channels and catalog sources, control install-plan approval, and consider upgrade windows. `Automatic` approval is convenient but can move production clusters unexpectedly when channels update.


### Commands / manifests

```bash
oc api-resources | grep -i operatorpolicy
oc explain operatorpolicy.spec --recursive
oc get operatorpolicies.policy.open-cluster-management.io -A
```


### L3 / production considerations

Separate “operator must be present” from “operator custom resource must be configured.” Validate CSV health, operand health, and application-level function. A succeeded CSV does not guarantee the operator's managed service is healthy.


---

## Q37. How can RHACM deploy and use the Compliance Operator across clusters?

### Detailed answer

Use governance to install the Compliance Operator consistently, create scan resources, and report compliance. A common pattern is:

1. Operator policy ensures the Compliance Operator is installed.
2. Configuration policy creates `ScanSetting`, `ScanSettingBinding`, or tailored profile resources.
3. The Compliance Operator creates `ComplianceSuite` and `ComplianceScan` objects and runs scans locally.
4. RHACM policy status reports whether required scan resources and results meet the declared expectation.

Do not confuse RHACM policy compliance with the internal result of every Compliance Operator rule. RHACM can assert that the scan is installed, ran, and produced expected status; detailed rule results remain Compliance Operator resources on each cluster.


### Commands / manifests

```bash
# Managed cluster
oc get csv -n openshift-compliance
oc get scansettings,scansettingbindings -n openshift-compliance
oc get compliancesuites,compliancescans -n openshift-compliance
oc get compliancecheckresults -n openshift-compliance
```


### L3 / production considerations

Schedule scans to avoid simultaneous load across the fleet. Define profile/version pinning, exception ownership, result retention, and remediation review. Automatically applying remediations without testing can change kubelet, API server, or platform configuration.


---

## Q38. Explain ScanSettingBinding, ComplianceSuite, and ComplianceScan.

### Detailed answer

A `ScanSettingBinding` associates one or more compliance profiles with a scan setting. The Compliance Operator translates that intent into a `ComplianceSuite`, which coordinates one or more `ComplianceScan` objects. Each scan evaluates a profile against the target cluster or nodes and produces results such as `ComplianceCheckResult` and possible remediations.

A scan can be `RUNNING`, `DONE`, `ERROR`, or another state depending on version. A completed suite may still be noncompliant. Therefore administrators must separate execution health from security result.

Node scans may require privileged collector pods and can take time. Scheduling, node availability, and storage affect scan completion.


### Commands / manifests

```bash
oc describe scansettingbinding <name> -n openshift-compliance
oc get compliancesuite -n openshift-compliance -o wide
oc describe compliancescan <name> -n openshift-compliance
oc get compliancecheckresult -n openshift-compliance \
  -l compliance.openshift.io/scan-name=<scan>
```


### L3 / production considerations

Track three dimensions separately: scan execution failure, policy noncompliance, and approved exceptions. Do not label an operational scan error as a security failure or vice versa.


---

## Q39. How do you manage policy exceptions without weakening fleet governance?

### Detailed answer

Exceptions must be explicit, time-bound, owned, and auditable. Avoid editing the global policy so every cluster becomes exempt. Better patterns include:

- Label the approved exception cluster and exclude it with a narrowly scoped placement expression.
- Create a separate exception policy set with documented rationale.
- Use tailored compliance profiles where supported.
- Store expiry date, ticket, owner, and compensating control in an external system and optionally labels/annotations.
- Alert when exceptions approach expiry.

The exception process should require risk acceptance and regular review. “Temporary” exclusions are a major source of permanent drift.


### Commands / manifests

```bash
oc label managedcluster <cluster> policy-exception.example.com/require-fips=INC12345 --overwrite
oc annotate managedcluster <cluster> policy-exception-expiry=2026-09-01 --overwrite
```


### L3 / production considerations

Do not place sensitive vulnerability details in broadly visible labels. Use opaque ticket references and protect the source-of-truth system.


---

## Q40. How do you troubleshoot a policy that remains Pending?

### Detailed answer

A pending policy usually has not completed propagation or evaluation. Check:

1. Is the root `Policy` enabled?
2. Does a valid `PlacementBinding` reference the correct policy or set?
3. Does the placement produce decisions?
4. Are replicated policies created in target cluster namespaces?
5. Is the governance add-on available on each target cluster?
6. Are policy controllers running?
7. Is the embedded policy template valid and supported?

If no replicated policy exists, focus on hub placement/binding/propagator. If it exists but has no status, focus on governance add-on and managed-cluster controllers.


### Commands / manifests

```bash
oc get policy <name> -n <ns> -o yaml
oc get placementbinding -n <ns> -o yaml
oc get placementdecision -n <ns> -o yaml
oc get policy -n <managed-cluster-name>
oc get managedclusteraddon governance-policy-framework -n <managed-cluster-name> -o yaml
oc logs -n open-cluster-management-agent-addon deploy/governance-policy-framework
```


### L3 / production considerations

Use timestamps to identify where propagation stopped. Avoid toggling remediation or recreating the policy until the broken stage is identified.


---

## Q41. How do you troubleshoot a NonCompliant ConfigurationPolicy?

### Detailed answer

Read the compliance message before changing anything. Determine the exact object, namespace, and mismatch. Then inspect that object on the managed cluster and compare it with the policy's compliance type.

Common causes:

- Object missing for `musthave`.
- Object exists for `mustnothave`.
- Field mismatch or type mismatch.
- `mustonlyhave` conflicts with defaulted fields.
- Admission webhook mutates the object.
- Another controller reverts the enforced change.
- The policy service account lacks permission.
- Template values resolve incorrectly.

For enforce loops, identify every controller owner through `managedFields`, owner references, GitOps annotations, and operator documentation.


### Commands / manifests

```bash
# Hub
oc describe policy <name> -n <ns>
oc get policy <name> -n <managed-cluster-name> -o yaml

# Managed cluster
oc get <kind> <name> -n <namespace> -o yaml --show-managed-fields
oc get events -n <namespace> --sort-by=.lastTimestamp
oc logs -n open-cluster-management-agent-addon deploy/config-policy-controller --since=30m
```


### L3 / production considerations

Never promote to `enforce` merely to “make green.” First confirm the desired state is valid and ownership is unambiguous.


---

## Q42. How does PolicyAutomation integrate remediation workflows?

### Detailed answer

`PolicyAutomation` can trigger automation, commonly through Ansible Automation Platform integration, when a policy becomes noncompliant. It is useful when remediation requires procedural logic that cannot or should not be represented as direct Kubernetes object enforcement.

Examples include opening an incident, gathering diagnostics, rotating an external credential, or invoking a reviewed playbook. Automation must be idempotent and protected against alert storms. A policy that flaps can otherwise trigger repeated disruptive jobs.

Separate detection from remediation approval for high-risk changes. Use manual mode or approval gates when the automation touches infrastructure, identity, data, or networking.


### Commands / manifests

```bash
oc api-resources | grep -i policyautomation
oc get policyautomation -A
oc describe policyautomation <name> -n <ns>
```


### L3 / production considerations

Implement deduplication, cooldown, job timeout, retry policy, credential isolation, and full audit logging. Confirm what happens when the automation platform is unavailable.


---

## Q43. How do you deliver governance policies through GitOps and Policy Generator?

### Detailed answer

Policy Generator is a Kustomize generator plug-in that converts Kubernetes manifests into RHACM policies and can generate PolicySets and placement-related resources. It allows governance to follow pull-request review, versioning, promotion, and rollback workflows.

Repository pattern:

- `base/`: common Kubernetes manifests and policy-generator configuration.
- `overlays/dev`, `overlays/prod`: environment-specific enforcement, labels, and placement.
- Separate directories for mandatory controls, optional controls, and exceptions.
- CI validates YAML, Kustomize build, policy schema, and prohibited resources.

Argo CD syncs the generated root policies to the hub. RHACM then propagates them to selected clusters. This creates two reconciliation layers; troubleshooting must distinguish Git-to-hub sync from hub-to-managed-cluster propagation.


### Commands / manifests

```bash
kustomize build overlays/prod > /tmp/rendered-policies.yaml
oc apply --server-side --dry-run=server -f /tmp/rendered-policies.yaml
oc diff -f /tmp/rendered-policies.yaml
```


### L3 / production considerations

Never store rendered secrets in Git or CI artifacts. Pin generator versions and test output changes during upgrades. Use Argo CD sync waves or policy dependencies deliberately when order matters.


---

## Q44. Explain RHACM observability architecture.

### Detailed answer

RHACM observability is an optional fleet-monitoring layer. Managed-cluster collectors gather an allowed set of metrics and forward them securely to hub-side observability endpoints. The hub runs Thanos-related components for receive, store, query, compaction, and rule evaluation, plus Grafana integration. Long-term metric blocks are stored in supported object storage.

Key design points:

- Observability is not automatically enabled by the base RHACM installation.
- Persistent/object storage is required.
- Metrics are filtered to control volume and cardinality.
- Certificates provide secure communication between managed clusters and hub.
- The hub can provide fleet dashboards and centralized alerts.

It complements, rather than necessarily replaces, per-cluster OpenShift monitoring. Local cluster monitoring remains valuable for deep cluster-specific diagnostics and alert routing.


### Commands / manifests

```bash
oc get multiclusterobservability -A
oc get pods -n open-cluster-management-observability
oc get pvc -n open-cluster-management-observability
oc get managedclusteraddon observability-controller -A
```


### L3 / production considerations

Capacity is driven by samples per second, label cardinality, retention, compaction, query concurrency, and object-store performance. Control cardinality before adding hardware.


---

## Q45. How do you enable observability?

### Detailed answer

Prepare a supported object store and create the required object-storage secret in `open-cluster-management-observability`. Then create a `MultiClusterObservability` custom resource. The operator deploys hub components and enables observability add-ons for eligible managed clusters.

The object-store secret format depends on the provider and uses Thanos object-storage configuration. Protect it as a high-value credential. Use least privilege, server-side encryption, network restrictions, versioning/object lock where appropriate, and a documented rotation process.

After creation, validate the custom resource conditions, pods, PVCs, routes, add-ons, object-store writes, and recent metrics from at least one managed cluster.


### Commands / manifests

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: open-cluster-management-observability
---
apiVersion: v1
kind: Secret
metadata:
  name: thanos-object-storage
  namespace: open-cluster-management-observability
type: Opaque
stringData:
  thanos.yaml: |
    type: S3
    config:
      bucket: rhacm-metrics
      endpoint: s3.example.com
      access_key: REPLACE_ME
      secret_key: REPLACE_ME
      insecure: false
---
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
  storageConfig:
    metricObjectStorage:
      name: thanos-object-storage
      key: thanos.yaml
```

```bash
oc apply -f observability.yaml
oc get mco observability -o yaml
oc get pods -n open-cluster-management-observability
```


### L3 / production considerations

Never use placeholder credentials in real manifests or Git. Prefer a secret operator or external secret injection. Test object-store permissions with a nonproduction bucket before enabling fleet ingestion.


---

## Q46. How do you validate that managed-cluster metrics are reaching the hub?

### Detailed answer

Validate from both ends.

On the managed cluster, confirm the observability add-on and collector pods are ready, certificates are valid, and logs show successful remote-write activity. On the hub, confirm receiver pods accept data, object storage receives blocks, Thanos query can return recent series labeled with the managed cluster, and dashboards show current timestamps.

Use a known metric such as cluster version, node status, or API availability. Query for one cluster and verify that the newest sample timestamp is recent. A dashboard showing old data can look healthy while ingestion has stopped.


### Commands / manifests

```bash
# Hub
oc get managedclusteraddon observability-controller -n <cluster> -o yaml
oc logs -n open-cluster-management-observability -l app.kubernetes.io/name=thanos-receive --since=30m
oc get pods -n open-cluster-management-observability

# Managed cluster
oc get pods -n open-cluster-management-addon-observability
oc logs -n open-cluster-management-addon-observability -l component=metrics-collector --since=30m
```


### L3 / production considerations

Alert on freshness, not only pod readiness. Define a maximum acceptable ingestion lag per fleet tier.


---

## Q47. How do you customize collected metrics while controlling cardinality?

### Detailed answer

RHACM supports allow-list customization for metrics collected from managed clusters. Customization is typically stored in a ConfigMap in the observability namespace. Exact keys and syntax must match the installed RHACM version.

Before adding a metric, estimate:

- Number of series per cluster.
- Labels with unbounded values, such as pod UID, request path, user, or container hash.
- Scrape frequency and retention.
- Query and dashboard use case.
- Whether the metric already exists under another name.

Add a small set, measure ingestion and storage impact, then expand. Removing a metric stops future collection but does not immediately erase historical blocks.


### Commands / manifests

```bash
oc get configmap observability-metrics-custom-allowlist \
  -n open-cluster-management-observability -o yaml
oc edit configmap observability-metrics-custom-allowlist \
  -n open-cluster-management-observability
```


### L3 / production considerations

Establish a metric review board or lightweight approval process. One high-cardinality metric multiplied across hundreds of clusters can destabilize the entire observability service.


---

## Q48. How do you customize Alertmanager and alert routing?

### Detailed answer

Central alerts should be routed by severity, environment, team, and service ownership. Store Alertmanager configuration in the supported RHACM secret/configuration object and protect receiver credentials.

Design principles:

- Use inhibition to suppress symptom alerts when a root-cause alert fires.
- Group related alerts to avoid notification storms.
- Route production and nonproduction differently.
- Add ownership labels to alert rules.
- Test receiver connectivity and fallback paths.
- Avoid embedding passwords or webhook tokens in Git.

Central alerting must not create a single blind spot. Critical managed clusters should retain local alerting for conditions that must be detected even when hub connectivity is lost.


### Commands / manifests

```bash
oc get secrets -n open-cluster-management-observability | grep alert
oc get prometheusrules -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability | grep alert
```


### L3 / production considerations

Measure notification latency and false-positive rate. Use maintenance silences with expiry and ownership; never leave indefinite silences undocumented.


---

## Q49. How do you troubleshoot object-storage failures in observability?

### Detailed answer

Symptoms include receiver upload errors, compactor failures, store-gateway errors, missing historical data, or increasing local disk usage.

Check:

1. Secret syntax and provider endpoint.
2. DNS, TLS trust, proxy, and firewall.
3. Bucket existence and region.
4. Permissions for list, get, put, delete, and multipart operations required by the component.
5. Object-store throttling or quota.
6. Clock skew and signature errors.
7. Encryption/KMS permissions.
8. Pod logs and recent credential rotation.

Do not rotate or overwrite the secret blindly during an incident. Confirm which component uses which credential and whether pods reload it automatically.


### Commands / manifests

```bash
oc get secret thanos-object-storage -n open-cluster-management-observability -o yaml
oc logs -n open-cluster-management-observability -l app.kubernetes.io/name=thanos-store --since=30m
oc logs -n open-cluster-management-observability -l app.kubernetes.io/name=thanos-compact --since=30m
oc get events -n open-cluster-management-observability --sort-by=.lastTimestamp
```


### L3 / production considerations

Enable bucket versioning and independent monitoring. Object-storage availability and immutability are part of the observability SLO and security model.


---

## Q50. How do you troubleshoot Grafana showing no data?

### Detailed answer

Work from query to storage rather than restarting Grafana first.

- Confirm the selected time range and cluster variable.
- Run the panel query directly and inspect errors.
- Confirm Thanos Query is reachable and healthy.
- Check whether the metric is in the allow list.
- Verify recent samples are arriving from the managed cluster.
- Check receiver, store, query-frontend, and object-store logs.
- Confirm user RBAC and tenant filtering.
- Compare with a known built-in dashboard.

If recent data is missing but historical data exists, focus on collectors/receive. If recent data exists but historical data is missing, focus on compaction/store/object storage. If raw queries work but the dashboard fails, focus on variables and panel expressions.


### Commands / manifests

```bash
oc get pods -n open-cluster-management-observability
oc logs -n open-cluster-management-observability -l app.kubernetes.io/name=thanos-query --since=30m
oc logs -n open-cluster-management-observability -l app.kubernetes.io/name=grafana --since=30m
```


---

## Q51. How do you plan observability retention and capacity?

### Detailed answer

Estimate capacity from active series, sample rate, bytes per sample after compression, block/index overhead, replication, retention, and growth. Include object-store request cost and data transfer, not only capacity.

Retention should reflect operational and compliance use cases. High-resolution recent data may be needed for incident response, while longer-term capacity planning may use recording rules or downsampled/aggregated data where supported.

Control growth with metric allow lists, label hygiene, scrape interval, recording rules, and deletion of unused dashboards/alerts. Scaling pods cannot compensate indefinitely for unbounded cardinality.


### L3 / production considerations

Track top metrics by series count, top labels by cardinality, ingestion rate, receiver backpressure, compaction duration, query latency, cache hit rate, object-store errors, and bucket growth. Review monthly and before onboarding large fleets.


---

## Q52. What happens to managed clusters if the observability hub is down?

### Detailed answer

Managed clusters continue running workloads and their local OpenShift monitoring remains independent. Central dashboards, cross-cluster queries, and hub-side alert evaluation become unavailable or stale. Collectors may retry and buffer only within their designed limits; prolonged outages can create gaps.

This is why critical alerts should not rely exclusively on the hub. Maintain local alerting for API, etcd, node, storage, and business-critical workload failures. The hub provides aggregation and fleet intelligence, not the only safety mechanism.

After recovery, verify sample freshness, receiver backlog, object-store writes, compaction, and alert evaluation. Do not assume data automatically backfills for the entire outage.


### L3 / production considerations

Define RTO/RPO separately for cluster management and fleet observability. A hub outage has a different business impact from a managed-cluster outage.


---

## Q53. Explain Kustomize bases, overlays, patches, and generators.

### Detailed answer

Kustomize builds Kubernetes manifests without templating every line. A **base** contains reusable resources. An **overlay** references the base and applies environment-specific patches, labels, names, images, or generators.

Common elements:

- `resources`: included YAML or other Kustomize directories.
- `patches`: strategic merge or JSON6902-style changes.
- `images`: replace image name or tag/digest.
- `configMapGenerator` and `secretGenerator`: generate resources with content hashes.
- `components`: reusable optional configuration.

Use overlays for meaningful environment differences, not uncontrolled copy/paste. Render and inspect output before syncing. Generated secret content should not be stored in plaintext Git.


### Commands / manifests

```bash
kustomize build overlays/dev
oc kustomize overlays/prod | oc apply --server-side --dry-run=server -f -
oc diff -k overlays/prod
```


### L3 / production considerations

Pin remote bases or avoid them in restricted environments. A changing remote `main` branch makes builds nonreproducible. Validate deprecated Kustomize fields during OpenShift upgrades.


---

## Q54. How should a Git repository be organized for multicluster GitOps?

### Detailed answer

A scalable repository separates application source, environment configuration, and fleet placement. One pattern is:

```text
apps/
  payments-api/base/
  payments-api/overlays/dev/
  payments-api/overlays/prod/
clusters/
  dev/
  prod-eu/
placements/
  payments-prod/
policies/
  baseline/
```

Use protected branches, CODEOWNERS, signed commits where required, CI validation, immutable image digests, and environment promotion through pull requests. Keep credentials outside Git.

Avoid one enormous repository if ownership, access, or blast radius differs. Conversely, avoid hundreds of tiny repositories that make coordinated changes impossible. Choose boundaries based on team ownership and release lifecycle.


### L3 / production considerations

Document reconciliation ownership: which Argo CD instance watches which path, which namespace contains the application, which placement selects clusters, and who approves production changes.


---

## Q55. How do you install OpenShift GitOps by using RHACM governance?

### Detailed answer

Use an `OperatorPolicy` or a set of configuration policies to create the operator namespace, `OperatorGroup`, and `Subscription` on selected clusters. Then create or configure the Argo CD instance according to your architecture.

The policy should initially report in `inform`. After verifying catalog availability, channels, storage, and resource requirements, move to `enforce` for canary clusters. Separate operator installation from Argo CD instance configuration so failures are easier to isolate.

For the hub-side GitOps integration, OpenShift GitOps is often installed on the hub and used to deploy applications or policies. Managed clusters can be imported into that Argo CD instance through a `GitOpsCluster` workflow.


### Commands / manifests

```bash
oc get policies -A | grep -i gitops
oc get subscription,csv -n openshift-operators | grep -i gitops
oc get argocd -A
oc get pods -n openshift-gitops
```


### L3 / production considerations

Use manual install-plan approval for production if operator upgrades require change windows. Back up Argo CD configuration and externalize repository/cluster credentials securely.


---

## Q56. What is GitOpsCluster and how are managed clusters imported into Argo CD?

### Detailed answer

A `GitOpsCluster` connects an Argo CD server with a `Placement`. The RHACM GitOps integration watches placement decisions and creates or updates Argo CD cluster secrets for selected managed clusters. This avoids manually copying kubeconfigs for every cluster.

Required pieces typically include:

- OpenShift GitOps/Argo CD instance.
- `ManagedClusterSetBinding` in the integration namespace.
- `Placement` selecting managed clusters.
- `GitOpsCluster` referencing the placement and Argo CD server.
- Correct service-account and RBAC permissions.

When a cluster leaves placement, understand whether and when its Argo CD credential is removed and what happens to already deployed applications. Do not assume workload deletion unless application pruning and destination behavior are configured.


### Commands / manifests

```yaml
apiVersion: apps.open-cluster-management.io/v1beta1
kind: GitOpsCluster
metadata:
  name: production-gitops
  namespace: openshift-gitops
spec:
  argoServer:
    cluster: local-cluster
    argoNamespace: openshift-gitops
  placementRef:
    kind: Placement
    apiVersion: cluster.open-cluster-management.io/v1beta1
    name: production-placement
```

```bash
oc get gitopscluster -A
oc get placementdecision -n openshift-gitops
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```


### L3 / production considerations

Cluster-import secrets are powerful. Restrict Argo CD namespace access, encrypt etcd, rotate credentials, and use narrowly scoped service accounts where supported.


---

## Q57. How does an ApplicationSet use RHACM Placement decisions?

### Detailed answer

The Argo CD ApplicationSet controller can use a cluster-decision-resource generator. It reads `PlacementDecision` objects produced by RHACM and generates one Argo CD `Application` per selected cluster.

The pattern separates concerns:

- RHACM decides eligible clusters.
- ApplicationSet creates application instances.
- Argo CD reconciles manifests to each destination.

Template fields use decision data such as cluster name and server. Repository path can also be parameterized by labels or environment. Test generated applications before enabling automated prune/self-heal.


### Commands / manifests

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: payments
  namespace: openshift-gitops
spec:
  generators:
  - clusterDecisionResource:
      configMapRef: acm-placement
      labelSelector:
        matchLabels:
          cluster.open-cluster-management.io/placement: payments-placement
      requeueAfterSeconds: 180
  template:
    metadata:
      name: 'payments-{{name}}'
    spec:
      project: default
      source:
        repoURL: https://git.example.com/platform/payments-config.git
        targetRevision: main
        path: overlays/prod
      destination:
        server: '{{server}}'
        namespace: payments
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```


### L3 / production considerations

`prune: true` can delete resources after a placement or repository change. Use sync windows, canaries, and deletion safeguards for critical applications. Consider stateful data and external resources before enabling automated prune.


---

## Q58. What is the difference between RHACM application resources and Argo CD applications?

### Detailed answer

RHACM historically provided application lifecycle resources such as `Application`, `Channel`, `Subscription`, and placement rules. Modern enterprise workflows commonly use OpenShift GitOps/Argo CD and ApplicationSet, integrated with RHACM placement and cluster import.

The RHACM console can provide a multicluster application view, but the actual reconciliation owner must be clear. Running legacy subscriptions and Argo CD against the same resources can create duplicate ownership and drift loops.

For new implementations, follow the product version's recommended GitOps integration. For existing legacy resources, plan migration carefully: inventory subscriptions, channels, placement rules, deployed resources, and pruning behavior.


### Commands / manifests

```bash
oc get applications.app.k8s.io -A
oc get channels,subscriptions.apps.open-cluster-management.io -A
oc get applications.argoproj.io,applicationsets.argoproj.io -A
```


### L3 / production considerations

Choose one source of truth per resource. Add ownership labels and document whether RHACM policy, legacy subscription, Argo CD, Helm operator, or another controller is authoritative.


---

## Q59. How do you troubleshoot an Argo CD application that is OutOfSync on only one managed cluster?

### Detailed answer

Because other clusters sync correctly, shared Git and ApplicationSet logic are probably healthy. Focus on the destination-specific path:

- Argo CD cluster secret and credential validity.
- Destination API reachability and TLS.
- Namespace existence and project destination permission.
- Different API availability or operator CRDs.
- Admission webhook, SCC, quota, or policy rejection.
- Existing resource ownership conflict.
- Cluster-specific Kustomize overlay or label-driven path.

Use Argo CD diff and application events, then inspect the exact resource on the destination cluster.


### Commands / manifests

```bash
argocd app get <app>
argocd app diff <app>
argocd app sync <app> --dry-run
oc get application <app> -n openshift-gitops -o yaml
oc logs -n openshift-gitops deploy/openshift-gitops-application-controller --since=30m
```


### L3 / production considerations

Do not click “force sync” repeatedly. Capture the comparison error and destination admission message first. A force replace can cause downtime for immutable resources.


---

## Q60. How do you troubleshoot a managed cluster missing from Argo CD?

### Detailed answer

Trace the integration chain:

1. `ManagedCluster` is available.
2. Cluster belongs to a set bound in the GitOps namespace.
3. `Placement` returns the cluster in a `PlacementDecision`.
4. `GitOpsCluster` references the correct placement and Argo namespace/server.
5. GitOps integration controller is healthy.
6. Argo CD cluster secret is created and valid.
7. Argo CD recognizes the cluster.

If placement is correct but no secret exists, focus on GitOpsCluster controller logs and RBAC. If the secret exists but Argo CD shows unknown/unreachable, inspect server URL, token, CA, and network.


### Commands / manifests

```bash
oc get managedclustersetbinding -n openshift-gitops
oc get placement,placementdecision -n openshift-gitops -o yaml
oc get gitopscluster -n openshift-gitops -o yaml
oc get secret -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get pods -n open-cluster-management | grep -i gitops
```


---

## Q61. How do you design safe automated sync, prune, and self-heal?

### Detailed answer

Automated sync applies Git changes without manual intervention. Self-heal reverts live drift. Prune deletes resources removed from Git. Together they provide strong consistency but can amplify mistakes quickly across a fleet.

Controls should include:

- Pull-request approval and CODEOWNERS.
- CI rendering and schema validation.
- Policy checks for dangerous resources.
- Canary placements and staged promotion.
- Argo CD projects limiting repositories, destinations, and resource kinds.
- Sync windows for production.
- Resource annotations or waves for ordering.
- Backup and recovery for stateful components.
- Deletion confirmation or preservation policy for critical resources.

For cluster-wide operators, CRDs, RBAC, or storage, start with manual sync and no prune.


### L3 / production considerations

Measure change failure rate, reconciliation duration, sync error types, and rollback time. Practice reverting Git and handling resources that cannot be rolled back simply, such as schema migrations.


---

## Q62. How do you use Git from the CLI effectively in an EX432 or production workflow?

### Detailed answer

The practical Git skills are clone, inspect status, branch, edit, diff, commit, pull/rebase according to policy, and push. Always verify the branch and repository before modifying manifests.

Use small commits with meaningful messages. Render Kustomize output before commit. Avoid committing kubeconfigs, tokens, `.env` files, or rendered secrets. In a timed lab, use `git diff` and `git status` before push to catch wrong paths and indentation errors.


### Commands / manifests

```bash
git clone https://git.example.com/platform/fleet-config.git
cd fleet-config
git switch -c add-prod-placement
kustomize build overlays/prod >/tmp/rendered.yaml
oc apply --server-side --dry-run=server -f /tmp/rendered.yaml
git status
git diff
git add .
git commit -m 'Add production placement for payments'
git push -u origin add-prod-placement
```


### L3 / production considerations

Use signed commits/tags where required and protect main branches. Secrets found in Git must be considered compromised even if removed later; rotate them and purge history through an approved process.


---

## Q63. How do you troubleshoot Kustomize output that works locally but fails in Argo CD?

### Detailed answer

Compare the build environments:

- Kustomize version embedded in Argo CD versus local binary.
- Enabled plug-ins and load restrictions.
- Repository credentials and submodules.
- Remote bases and network access.
- Case-sensitive file paths.
- Helm integration flags if used.
- Environment variables or files available locally but absent in repo.

Render using the same tool version as Argo CD when possible. Inspect repo-server logs and the exact manifest-generation error. Do not patch the live application to bypass a repository problem; that creates drift and hides the cause.


### Commands / manifests

```bash
argocd app manifests <app> >/tmp/argocd-rendered.yaml
kustomize version
kustomize build <path> >/tmp/local-rendered.yaml
diff -u /tmp/local-rendered.yaml /tmp/argocd-rendered.yaml
oc logs -n openshift-gitops deploy/openshift-gitops-repo-server --since=30m
```


---

## Q64. Explain the role of RHACM in multicluster OpenShift Virtualization management.

### Detailed answer

RHACM provides fleet inventory, placement, governance, GitOps integration, search, and centralized visibility for clusters running OpenShift Virtualization. The OpenShift Virtualization operator and KubeVirt components still run on each managed cluster. RHACM does not replace local virtualization control-plane components.

A multicluster VM workflow can:

- Use governance/GitOps to install OpenShift Virtualization consistently.
- Apply cluster labels for hardware, region, storage, or compliance capability.
- Use placement to choose eligible virtualization clusters.
- Deploy VM manifests through Argo CD/ApplicationSet.
- Use RHACM search and dashboards to locate and monitor VMs across clusters.

VM placement must account for CPU features, storage class, DataVolume source access, network attachment definitions, and migration capability—not only cluster availability.


### Commands / manifests

```bash
oc get kubevirt -A
oc get hyperconverged -A
oc get virtualmachines,virtualmachineinstances -A
oc get datavolumes -A
oc get network-attachment-definitions -A
```


### L3 / production considerations

Define virtualization capability labels through automated discovery or controlled governance. Manual labels that claim a cluster has a storage/network capability can cause failed VM deployments.


---

## Q65. How do you deploy the OpenShift Virtualization Operator with GitOps?

### Detailed answer

Store the namespace, OperatorGroup, Subscription, and `HyperConverged` custom resource in Git, usually separated into ordered layers. Argo CD sync waves can install the operator first, wait for its CRDs/CSV, then apply the `HyperConverged` resource.

Use a placement that selects only clusters meeting OpenShift version, capacity, CPU virtualization, storage, and network prerequisites. Applying the operator to every cluster by default can waste resources or fail on unsupported infrastructure.

Control the operator channel and install-plan policy. Validate `KubeVirt`, CDI, network add-ons, SSP, and storage profile status after installation.


### Commands / manifests

```bash
oc get subscription,csv -n openshift-cnv
oc get hyperconverged -n openshift-cnv
oc get kubevirt,cdi,networkaddonsconfig,ssp -A
oc get pods -n openshift-cnv
```


### L3 / production considerations

For upgrades, coordinate OpenShift, OpenShift Virtualization, storage, and guest compatibility. Test live migration, snapshots, DataVolume import, and VM restart before broad rollout.


---

## Q66. How do you deploy VMs to selected clusters with ApplicationSet?

### Detailed answer

Represent VM-related resources in Git: namespace, DataVolume or image reference, VM, network attachment references, secrets references, and policies. A RHACM placement selects clusters with labels such as `virtualization=enabled`, `cpu-feature=required`, `storage-tier=gold`, and region. ApplicationSet creates an Argo CD Application per decision.

Do not embed unique MAC addresses, static IPs, cloud-init passwords, or SSH private keys in a shared base. Generate or inject per-cluster values securely. Use overlays for storage classes, networks, and regional image sources.

VM readiness should include DataVolume completion, VMI phase, guest-agent status where required, network reachability, and application health.


### Commands / manifests

```bash
oc get placementdecision -n openshift-gitops
oc get applicationset -n openshift-gitops
oc get virtualmachine -A
oc get virtualmachineinstance -A
oc get datavolume -A
```


### L3 / production considerations

Automated prune can delete VMs and attached resources. Use Argo CD resource-preservation controls and documented retirement workflows for stateful VMs.


---

## Q67. How do you locate and troubleshoot a VM across many clusters?

### Detailed answer

Start with RHACM search using VM name, namespace, labels, owner, or status. Identify the hosting cluster, then switch to that cluster for detailed KubeVirt diagnostics.

Check:

- `VirtualMachine` desired running state.
- `VirtualMachineInstance` phase and conditions.
- Launcher pod scheduling and events.
- DataVolume/PVC readiness.
- Node virtualization capability and taints.
- Network attachment definition and Multus errors.
- Cloud-init and guest-agent status.
- Migration or eviction strategy.

Fleet search is for discovery; local APIs provide authoritative detail.


### Commands / manifests

```bash
oc get vm,vmi -n <ns>
oc describe vm <name> -n <ns>
oc describe vmi <name> -n <ns>
oc get pod -n <ns> -l kubevirt.io=virt-launcher
oc get dv,pvc -n <ns>
oc get events -n <ns> --sort-by=.lastTimestamp
```


### L3 / production considerations

Tag VMs with application, owner, environment, data classification, backup tier, and RTO/RPO. Without metadata, fleet-level incident response becomes slow and risky.


---

## Q68. How do you monitor OpenShift Virtualization with RHACM?

### Detailed answer

Use RHACM observability to collect approved KubeVirt and VM-related metrics, then build fleet dashboards and alerts. Examples include VMI phase, VM start failures, migration failures, node virtualization capacity, DataVolume import failures, and virt-handler health.

Cardinality is a concern because VM name, namespace, node, and migration IDs can multiply series. Add only metrics tied to operational decisions. Retain local OpenShift Virtualization dashboards for detailed cluster diagnosis.

Central alerts should include cluster, namespace, VM, owner, and runbook labels. Route infrastructure failures to platform teams and guest/application failures to workload owners.


### Commands / manifests

```bash
oc get servicemonitors,podmonitors -n openshift-cnv
oc get prometheusrules -n openshift-cnv
oc get configmap observability-metrics-custom-allowlist \
  -n open-cluster-management-observability -o yaml
```


---

## Q69. How do you handle VM migration or failover across clusters?

### Detailed answer

KubeVirt live migration is normally within a cluster. Cross-cluster movement is a separate disaster-recovery or migration workflow involving VM definitions, disks, network identity, secrets, and application dependencies.

RHACM can orchestrate placement and GitOps deployment, but data replication and failover consistency require supported storage/DR tooling and explicit runbooks. Prevent both source and destination VMs from writing the same data unless the storage and application are designed for active-active operation.

A controlled failover includes quiescing or fencing the source, confirming replicated data state, updating placement/DR resources, starting the destination, validating networking/DNS, and recording recovery point/time.


### L3 / production considerations

Test failover and failback. A successful VM boot does not prove application consistency. Include database recovery, external IP/DNS, certificates, licenses, and downstream dependencies.


---

## Q70. Design a hub disaster-recovery strategy.

### Detailed answer

A hub DR strategy protects RHACM configuration and the ability to resume fleet management. The cluster backup and restore capability uses OADP/Velero integration to back up hub resources to object storage. Managed clusters continue running during a hub outage, but centralized policy changes, search, observability, and GitOps orchestration may be impaired.

Design elements:

- Backup schedule aligned to RPO.
- Independent, durable, protected object storage.
- Encryption and credential rotation.
- Documented replacement-hub prerequisites and version compatibility.
- Recovery of cluster registrations, policies, applications, and secrets according to supported scope.
- Separate recovery plans for observability data and hosted control planes/VM data.
- Regular restore tests in an isolated environment.

Do not assume hub backup includes every workload or managed-cluster application. It primarily protects management-plane resources.


### Commands / manifests

```bash
oc get dataprotectionapplication -A
oc get backups,restores,schedules -A
oc get clusterbackup,clusterrestore -A 2>/dev/null || true
oc get backupstoragelocations -A
```


### L3 / production considerations

Use immutable/versioned backup storage and separate credentials from the hub. Define ransomware and accidental-deletion protections. Record the exact order for recovering hosted control planes, hub management configuration, GitOps, and workloads.


---

## Q71. How do you diagnose fleet-wide failures versus a single-cluster failure?

### Detailed answer

Use scope as the first discriminator.

**Many clusters fail simultaneously:** suspect hub API/routes, DNS, load balancer, certificate expiry, proxy changes, hub resource exhaustion, registration/work controllers, object storage, or a common Git/registry dependency.

**One cluster fails:** suspect local API, agent pods, network path, certificate, proxy, image pull, cluster-specific RBAC, labels, or add-on state.

**One feature fails across all clusters:** suspect that feature's hub controller or shared dependency. For example, observability-only failure points to receive/object store/certificates, while policies continue working.

Build a timeline and compare last successful lease, policy evaluation, metric sample, and Git sync. This reveals the broken data path.


### Commands / manifests

```bash
oc get managedclusters
oc get managedclusteraddons -A
oc get mch,mce -A
oc get pods -n open-cluster-management
oc get events -A --sort-by=.lastTimestamp | tail -200
```


### L3 / production considerations

Do not restart every namespace. Restarting destroys evidence and can create load spikes. Isolate the failing service and dependency first.


---

## Q72. How do you handle controller conflicts between RHACM policy and Argo CD?

### Detailed answer

A conflict occurs when two controllers own the same field with different desired values. Symptoms include continuous policy enforcement, Argo CD `OutOfSync`, repeated updates, API load, and noisy events.

Resolve by choosing one owner per resource or field:

- Use Argo CD for application manifests and RHACM policies for compliance checks in `inform` mode.
- Use RHACM enforcement for platform baseline objects not managed by Argo CD.
- Exclude controller-owned fields from strict policy comparisons.
- Split resources so each controller owns different objects.
- Use documented ignore-difference rules only when the ignored drift is safe.

Inspect `managedFields`, labels, annotations, and controller logs to prove the conflict.


### Commands / manifests

```bash
oc get <kind> <name> -n <ns> -o yaml --show-managed-fields
argocd app diff <app>
oc describe policy <policy> -n <ns>
oc get events -n <ns> --sort-by=.lastTimestamp
```


### L3 / production considerations

Never solve ownership conflict by disabling drift detection globally. That hides real unauthorized changes.


---

## Q73. How do you secure credentials used by RHACM, GitOps, providers, and observability?

### Detailed answer

Classify credentials by blast radius: hub admin, managed-cluster registration, cloud provider, Git repository, Argo CD destination, object storage, automation platform, and external secret providers.

Controls include:

- Least-privilege service accounts and provider roles.
- Short-lived credentials where supported.
- Secrets encrypted at rest in OpenShift etcd.
- External secret management for high-value material.
- Restricted namespace RBAC.
- No plaintext credentials in Git, policy templates, logs, or support bundles.
- Rotation runbooks and tested reload behavior.
- Audit of secret reads and changes.
- Separate credentials for environments and functions.

A hub compromise can expose fleet-wide access, so hub secrets deserve tier-0 controls.


### Commands / manifests

```bash
oc get apiserver cluster -o yaml | grep -A8 encryption
oc auth can-i get secrets -n openshift-gitops --as=<user>
oc auth can-i get secrets -n open-cluster-management-observability --as=<user>
oc get rolebindings,clusterrolebindings -A | grep -i secret
```


### L3 / production considerations

Rotation testing must include controller behavior. Some pods reload mounted secrets; others require restart or resource reconciliation. Document each dependency before rotating in production.


---

## Q74. What should an RHACM L3 incident runbook contain?

### Detailed answer

The runbook should be symptom-oriented and evidence driven.

Minimum sections:

- Scope and business impact.
- Hub, managed-cluster, governance, observability, GitOps, and virtualization health checks.
- Known-good endpoints and test commands.
- Required logs, events, YAML, and timestamps.
- Safe mitigation versus destructive actions.
- Credential/certificate checks without exposing secrets.
- Escalation criteria and support-data collection.
- Recovery validation tests.
- Rollback and change-freeze procedures.
- Communication templates and ownership matrix.

Include exact namespace and resource discovery commands, but avoid hard-coding pod names that change after upgrades. Prefer label selectors and API ownership.


### L3 / production considerations

Exercise the runbook during game days. Measure time to detect, time to isolate component, time to mitigate, and evidence completeness. Update it after every real incident.


---

## Q75. What is a practical four-hour EX432 exam strategy?

### Detailed answer

Begin by reading all tasks and identifying dependencies. Complete foundational items—operator health, cluster import, cluster-set binding, and Git repository access—before tasks that depend on them.

Recommended habits:

- Verify `oc whoami`, current context, and namespace before every major command.
- Use `oc explain` for API versions and fields.
- Copy provided starter files before editing.
- Use `oc apply --dry-run=server` and `oc diff`.
- Check status conditions after each task, not only at the end.
- Keep a scratch file of completed tasks and validation commands.
- Prefer declarative YAML over one-off imperative changes.
- Recheck persistence and final state during the last review.

Do not spend excessive time on one blocked task. Preserve partial valid configuration, move to independent tasks, and return later. The exam evaluates final state, not how elegant the command history looks.


### Commands / manifests

```bash
oc whoami
oc config current-context
oc project
oc api-resources | grep -i <keyword>
oc explain <resource>.spec --recursive
oc apply --server-side --dry-run=server -f file.yaml
oc diff -f file.yaml
oc get events -A --sort-by=.lastTimestamp | tail -50
```


---

## Q76. Why exclude `local-cluster` from some placements?

**Answer:** Because it is the hub itself. A broad placement can unintentionally enforce workload or security changes on the management plane and increase blast radius.

---

## Q77. What object proves Placement output?

**Answer:** `PlacementDecision`. Inspect its decisions and labels rather than assuming the selector matched.

---

## Q78. What object proves hub-to-cluster work delivery?

**Answer:** `ManifestWork` in the managed cluster namespace on the hub, together with its applied/available status conditions.

---

## Q79. Can a Running pod still represent an unhealthy RHACM component?

**Answer:** Yes. Readiness may fail, logs may show reconciliation errors, dependencies may be unavailable, or status may be stale.

---

## Q80. Why start governance in inform mode?

**Answer:** It measures impact and identifies false positives or controller conflicts before automatic changes are made.

---

## Q81. Why is `mustonlyhave` risky?

**Answer:** Controllers and admission webhooks often add default fields. Strict comparison can create constant enforcement loops or remove legitimate configuration.

---

## Q82. What is the first check for a missing Argo CD destination cluster?

**Answer:** Verify the RHACM PlacementDecision, then trace GitOpsCluster creation of the Argo CD cluster secret.

---

## Q83. What is the first check for stale dashboards?

**Answer:** Query the newest sample timestamp for a known metric. Pod readiness alone does not prove current ingestion.

---

## Q84. Why not store provider credentials in a Policy YAML?

**Answer:** Policies and Git repositories are broadly replicated and reviewed. Plaintext credentials create a fleet-wide compromise path.

---

## Q85. What is eventual consistency in RHACM search?

**Answer:** Search reflects indexed data and may lag the authoritative managed-cluster API. Validate before destructive action.

---

## Q86. How do you test user permissions?

**Answer:** Use `oc auth can-i` with `--as` and the exact namespace/resource/verb; also verify controller-side and Argo CD project permissions.

---

## Q87. What does a healthy ManagedCluster not guarantee?

**Answer:** It does not guarantee governance, observability, GitOps, or other add-ons are healthy. Check each ManagedClusterAddOn.

---

## Q88. Why are cluster labels security sensitive?

**Answer:** They control policy and workload placement and can indirectly grant a team access to new destinations.

---

## Q89. What is the preferred source of truth for production manifests?

**Answer:** A reviewed Git repository with validation and controlled reconciliation, not unrecorded live edits.

---

## Q90. What is the danger of Argo CD automated prune?

**Answer:** A repository or placement mistake can delete live resources across multiple clusters, including stateful resources.

---

## Q91. What is the difference between scan completion and compliance?

**Answer:** A ComplianceScan can complete successfully while reporting noncompliant checks; execution health and security result are separate.

---

## Q92. Why keep local alerting when RHACM observability is enabled?

**Answer:** Hub or network failure can make centralized alerts unavailable while the managed cluster still needs immediate detection.

---

## Q93. What is the safest first response to an Unknown cluster?

**Answer:** Inspect conditions, leases, agent logs, connectivity, and certificates. Do not immediately delete and re-import.

---

## Q94. What should be pinned in GitOps?

**Answer:** Repository revisions, operator channels as appropriate, image digests, remote bases, and generator/tool versions needed for reproducibility.

---

## Q95. What is a controller ownership conflict?

**Answer:** Two reconcilers continually set the same field to different values, causing drift loops and API churn.

---

## Q96. What does a CSV in Succeeded state prove?

**Answer:** The operator installed successfully. It does not prove its operands, custom resources, or business function are healthy.

---

## Q97. Why test hub backup restores?

**Answer:** A backup file proves little without a successful restore, version-compatible hub, credentials, and validated cluster reconnection.

---

## Q98. What should trigger fleet upgrade stop conditions?

**Answer:** Critical operator degradation, workload SLO breach, API/ingress/storage failures, or blocked node drains in the canary ring.

---

## Q99. How do you reduce observability cost most effectively?

**Answer:** Control metric and label cardinality first, then tune retention and scale infrastructure based on measured demand.

---

## Q100. What is the most important L3 habit in RHACM?

**Answer:** Trace the full reconciliation chain and identify the exact broken boundary before changing resources or restarting controllers.

---


# Command and Resource Cheat Sheet

## Hub installation and health

```bash
oc get subscription,csv,installplan -n open-cluster-management
oc get mch -n open-cluster-management -o yaml
oc get mce -A -o yaml
oc get deploy,sts,pods -n open-cluster-management
oc get events -n open-cluster-management --sort-by=.lastTimestamp
```

## Managed cluster registration

```bash
oc get managedclusters --show-labels
oc describe managedcluster <cluster>
oc get lease -n <cluster>
oc get managedclusteraddons -n <cluster>
oc get manifestworks -n <cluster>
```

## Agent-side checks

```bash
oc get pods -n open-cluster-management-agent
oc get pods -n open-cluster-management-agent-addon
oc logs -n open-cluster-management-agent deploy/klusterlet-registration-agent
oc logs -n open-cluster-management-agent deploy/klusterlet-work-agent
```

## ClusterSet and placement

```bash
oc get managedclustersets
oc get managedclustersetbindings -A
oc get placements,placementdecisions -A
oc get managedclusters --show-labels
```

## Governance

```bash
oc get policies,policysets -A
oc get placementbindings -A
oc describe policy <policy> -n <namespace>
oc get policy -n <managed-cluster-name>
oc get managedclusteraddon governance-policy-framework -n <managed-cluster-name>
```

## Compliance Operator

```bash
oc get csv -n openshift-compliance
oc get scansettings,scansettingbindings -n openshift-compliance
oc get compliancesuites,compliancescans -n openshift-compliance
oc get compliancecheckresults -n openshift-compliance
```

## Observability

```bash
oc get multiclusterobservability
oc get pods,pvc -n open-cluster-management-observability
oc get managedclusteraddon observability-controller -A
oc get configmap observability-metrics-custom-allowlist \
  -n open-cluster-management-observability -o yaml
```

## GitOps

```bash
oc get argocd,applications,applicationsets -A
oc get gitopsclusters -A
oc get placementdecision -n openshift-gitops
oc get secrets -n openshift-gitops \
  -l argocd.argoproj.io/secret-type=cluster
argocd app list
argocd app get <application>
argocd app diff <application>
```

## OpenShift Virtualization

```bash
oc get subscription,csv -n openshift-cnv
oc get hyperconverged,kubevirt,cdi -A
oc get vm,vmi,dv -A
oc get pods -n openshift-cnv
```

## Universal troubleshooting commands

```bash
oc whoami
oc config current-context
oc api-resources | grep -i <keyword>
oc explain <resource>.spec --recursive
oc get <resource> <name> -n <namespace> -o yaml
oc describe <resource> <name> -n <namespace>
oc get events -A --sort-by=.lastTimestamp | tail -100
oc auth can-i <verb> <resource> -n <namespace> --as=<user>
oc apply --server-side --dry-run=server -f file.yaml
oc diff -f file.yaml
```

# L3 Final Review Checklist

- [ ] I can install and validate RHACM through OLM.
- [ ] I can explain MCE, hub, klusterlet, ManifestWork, and add-ons.
- [ ] I can import, detach, provision, scale, and upgrade clusters safely.
- [ ] I can build ClusterSets, bindings, placements, and RBAC boundaries.
- [ ] I can create and troubleshoot governance policies and PolicySets.
- [ ] I can deploy and interpret Compliance Operator scans.
- [ ] I can enable, customize, capacity-plan, and troubleshoot observability.
- [ ] I can build Kustomize overlays and validate rendered YAML.
- [ ] I can integrate RHACM Placement with GitOpsCluster and ApplicationSet.
- [ ] I can deploy and monitor OpenShift Virtualization across selected clusters.
- [ ] I can distinguish hub-wide, feature-wide, and single-cluster incidents.
- [ ] I can prove persistence and final state with CLI validation.

# Official reference baseline

This guide was aligned to the Red Hat EX432 objectives published for OpenShift Container Platform 4.18 and RHACM 2.13, plus the official RHACM architecture, governance, observability, business-continuity, and course-outline documentation available on 1 August 2026. Product documentation may describe newer releases; use the exam page as the certification-version authority.

---

**End of guide**
