# OpenShift Administrator L3 — Complete Corporate Interview Q&A


---

<!-- Original file: 01. Architecture and Core Components.md -->

# OpenShift Administrator L3 Interview Q&A — Architecture and Core Components

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Control plane, Operators, etcd, RHCOS, APIs, reconciliation, and health.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain the OpenShift 4.x architecture and request flow.

    ### L3 answer

    OpenShift is a Kubernetes platform whose desired state is stored through the Kubernetes API and reconciled by controllers and Operators. A request from `oc`, the web console, or automation reaches the API endpoint, is authenticated, authorized by RBAC, evaluated by admission controls such as SCC and webhooks, and then persisted in etcd. Controllers create or update dependent resources. The scheduler selects a node, kubelet asks CRI-O to start containers, OVN-Kubernetes provides networking, CSI provides storage, and the Ingress stack exposes HTTP(S) applications.

OpenShift adds opinionated lifecycle management through the Cluster Version Operator, Cluster Operators, Machine Config Operator, integrated OAuth, Routes, internal registry, monitoring, and RHCOS. An L3 answer should show where a failure can occur instead of treating the platform as one component.

    ### How to prove it

    Use `oc get clusterversion`, `oc get clusteroperators`, `oc get nodes`, `oc get pods -A -o wide`, `oc get --raw='/readyz?verbose'`, and recent events. Trace a sample workload from Deployment to ReplicaSet, Pod, node, Service, EndpointSlice, and Route.

    ### Troubleshooting / operational approach

    Identify the first failed reconciliation stage: API/admission, controller, scheduling, image pull, CNI, CSI, kubelet, readiness, Service, or ingress. Inspect the owning resource and related Operator before changing generated objects.

    ### Production caution

    Do not restart or delete multiple platform components together. That destroys evidence and can expand the outage.

---

## Q2. What is the difference between Cluster Operators and OLM-managed Operators?

    ### L3 answer

    Cluster Operators manage core OpenShift capabilities and report `Available`, `Progressing`, `Degraded`, and sometimes `Upgradeable` conditions through ClusterOperator objects. They are part of the release payload and coordinated by the Cluster Version Operator.

OLM-managed Operators are add-on products installed through CatalogSource, Subscription, InstallPlan, ClusterServiceVersion, and OperatorGroup resources. OLM resolves dependencies and manages their lifecycle, but it does not manage the core Cluster Operators that form OpenShift itself.

    ### How to prove it

    For core health, run `oc get co` and `oc describe co <name>`. For OLM, run `oc get catalogsource,subscription,installplan,csv,operatorgroup -A` and follow the resource chain.

    ### Troubleshooting / operational approach

    For a core Operator, inspect its condition message and operand namespace. For OLM, check catalog availability, subscription resolution, InstallPlan approval, CSV phase, Operator deployment, CRDs, webhooks, and operand status.

    ### Production caution

    Never delete a core Cluster Operator. Avoid deleting CSVs or CRDs without vendor guidance because CRD deletion also removes custom resources.

---

## Q3. Explain etcd and why storage latency matters.

    ### L3 answer

    etcd is the strongly consistent key-value store that persists OpenShift and Kubernetes API state. It uses Raft consensus, so a majority of members must communicate before writes commit. In a common three-member topology, two healthy members are required for quorum.

etcd performs frequent small synchronous writes. Slow storage, packet loss, high control-plane CPU, filesystem pressure, time drift, or excessive API activity can increase commit latency and cause slow `oc` commands, API timeouts, leader changes, and cascading Operator degradation.

    ### How to prove it

    Check `oc get co etcd kube-apiserver`, etcd pods and nodes, control-plane CPU and memory, filesystem capacity, disk latency, and alerts. On a node debug shell, use `df -h`, `df -i`, and `iostat -xz 1` where available.

    ### Troubleshooting / operational approach

    Determine whether the issue is one member, quorum, storage, network, or API load. If one member is unhealthy and quorum remains, follow member-replacement guidance rather than restoring the whole cluster.

    ### Production caution

    An etcd restore is destructive and should not be used while targeted recovery is possible. Preserve the healthy majority and never reboot multiple control-plane nodes simultaneously.

---

## Q4. What do CVO and the release payload do?

    ### L3 answer

    The Cluster Version Operator reconciles the cluster toward a selected OpenShift release image. The release payload contains manifests and references for core platform components. CVO applies the required state, watches Cluster Operator conditions, and records update history in the ClusterVersion object.

An update is complete only when the target version is reached, required Operators are healthy, MachineConfigPools converge, nodes are ready, and critical platform and business tests pass.

    ### How to prove it

    Run `oc get clusterversion`, `oc describe clusterversion version`, `oc adm upgrade`, `oc get co`, `oc get mcp`, and `oc get nodes`.

    ### Troubleshooting / operational approach

    Find the Operator or pool still Progressing or Degraded and read its exact reason. Do not assume a long update is failed until you identify the component that is not converging.

    ### Production caution

    Do not force past `Upgradeable=False` without documented guidance and a formal risk decision.

---

## Q5. What is RHCOS and why are manual node changes risky?

    ### L3 answer

    Red Hat Enterprise Linux CoreOS is the immutable, container-optimized operating system used by OpenShift control-plane nodes and commonly workers. Ignition performs first-boot provisioning, while the Machine Config Operator manages supported host-level configuration and OS updates.

Direct edits, unmanaged RPM installation, and ad hoc systemd or kernel changes create drift. They can disappear after reboot, conflict with MCO, block upgrades, or leave one node behaving differently from the pool.

    ### How to prove it

    Inspect OS and kernel with `oc get nodes -o custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.osImage,KERNEL:.status.nodeInfo.kernelVersion`. Check `oc get mc`, `oc get mcp`, and node current/desired configuration annotations.

    ### Troubleshooting / operational approach

    Express persistent settings through MachineConfig, KubeletConfig, ContainerRuntimeConfig, Tuned, or another documented Operator API. Convert emergency manual changes into declarative configuration afterward.

    ### Production caution

    Do not treat SSH changes as permanent administration. Never delete files under CRI-O storage or MCO-managed paths as a shortcut.

---

## Q6. Explain control-plane quorum and failure tolerance.

    ### L3 answer

    A typical three-node control plane runs redundant API, scheduler, controller-manager, and etcd components. etcd needs a majority, so loss of one member is tolerated but loss of two removes quorum. API load balancing can hide failure of one API instance, but etcd majority loss affects the entire control plane.

Control-plane nodes should be distributed across independent failure domains while retaining low and predictable latency between them.

    ### How to prove it

    Check node and static-pod placement, `oc get co etcd kube-apiserver`, API readiness, and infrastructure health. Verify which members are healthy before any power operation.

    ### Troubleshooting / operational approach

    For a single failed node, preserve the two-member majority and recover or replace the failed member. For majority loss, freeze changes, engage support, and use the documented disaster-recovery procedure with a valid backup.

    ### Production caution

    Never perform parallel maintenance or reboot on multiple control-plane nodes unless the supported procedure explicitly allows it.

---

## Q7. What are static pods and how do you troubleshoot them?

    ### L3 answer

    Critical control-plane components such as etcd and API servers run as static pods managed by kubelet from node-local manifests. Operators generate and roll out revisions, while kubelet ensures the local static pods run.

Mirror pods are visible through the API, but the source manifest, node-local container state, kubelet, CRI-O, certificates, and filesystems are also part of troubleshooting.

    ### How to prove it

    Inspect pods in `openshift-etcd` and `openshift-kube-apiserver`, the related Cluster Operator, node journals, `crictl ps -a`, disk capacity, and revision-related resources.

    ### Troubleshooting / operational approach

    Determine whether the failure is a bad revision, expired certificate, disk full, container-runtime issue, kubelet failure, or host outage. Compare a failed node with a healthy control-plane node.

    ### Production caution

    Do not manually replace generated static-pod manifests unless following explicit Red Hat recovery guidance.

---

## Q8. How does reconciliation change troubleshooting?

    ### L3 answer

    Controllers continuously compare desired and actual state. A deleted resource may reappear because its owner recreated it, and a manual edit may be reverted by an Operator. Therefore, administrators must find the source of truth and owning controller.

Owner references, status conditions, events, managed fields, GitOps history, and controller logs reveal who controls the resource.

    ### How to prove it

    Use `oc get <resource> <name> -o yaml`, inspect `metadata.ownerReferences`, status conditions, events, and the parent custom resource or GitOps application.

    ### Troubleshooting / operational approach

    Fix the parent CR, Subscription, MachineConfig, or Git commit rather than repeatedly modifying generated Deployments, DaemonSets, or rendered configurations.

    ### Production caution

    Manual changes to generated operands can create a false recovery that disappears at the next reconciliation.

---

## Q9. Explain API groups, CRDs, and API deprecation.

    ### L3 answer

    Kubernetes APIs are identified by group, version, and kind. OpenShift extends Kubernetes through built-in APIs and CustomResourceDefinitions. A CRD defines a new resource type; an Operator or controller normally provides its behavior.

During upgrades, removed or deprecated APIs used by workloads, webhooks, automation, or Operators can block or break operations. API compatibility is therefore an upgrade dependency, not only a developer concern.

    ### How to prove it

    Use `oc api-resources`, `oc api-versions`, `oc explain`, `oc get crd`, and `oc get apirequestcounts`.

    ### Troubleshooting / operational approach

    Identify who is calling deprecated APIs, update manifests and clients, and verify no requests remain before the target upgrade.

    ### Production caution

    Do not assume that because an object still exists, its API version will be accepted after the next Kubernetes minor version.

---

## Q10. How do you determine whether a cluster is healthy?

    ### L3 answer

    Health is layered. Start with ClusterVersion and Cluster Operators, then validate nodes, MachineConfigPools, API readiness, DNS, ingress, storage, authentication, monitoring, CSRs, warning events, and representative business workloads.

`Available=True` for most Operators does not prove every tenant application works. Conversely, one optional degraded Operator may not mean total platform outage. State scope and impact.

    ### How to prove it

    Run `oc get clusterversion`, `oc get co`, `oc get nodes`, `oc get mcp`, `oc get csr`, `oc get pods -A --field-selector=status.phase!=Running,status.phase!=Succeeded`, and an end-to-end canary test.

    ### Troubleshooting / operational approach

    Use a canary that validates image pull, scheduling, service DNS, route ingress, and storage. Compare current state with a known baseline and recent changes.

    ### Production caution

    Do not declare recovery only because pods are Running. Validate readiness and a real transaction.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/overview/welcome-index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/architecture/control-plane
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/operators/index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/etcd/overview-of-etcd
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/release_notes/ocp-4-21-release-notes

---

<!-- Original file: 02. Installation and Infrastructure Readiness.md -->

# OpenShift Administrator L3 Interview Q&A — Installation and Infrastructure Readiness

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Install methods, bootstrap, DNS, load balancers, Ignition, disconnected operation, and acceptance.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Compare IPI, UPI, Assisted Installer, and Agent-based installation.

    ### L3 answer

    IPI lets the installer create and manage much of the infrastructure on supported platforms. UPI requires the customer to prepare and operate resources such as compute, DNS, load balancers, and networking. Assisted Installer provides guided discovery and validation. Agent-based installation creates bootable assets for on-premises or disconnected workflows.

The decision affects day-2 machine management, automation, ownership, and support responsibilities. UPI offers control but transfers more operational burden to the platform team.

    ### How to prove it

    Review install-config, platform support, Machine API resources, infrastructure ID, DNS, load balancers, firewall, and the documented installation method used.

    ### Troubleshooting / operational approach

    For failures, trace host discovery, ignition, API reachability, bootstrap, control-plane formation, Operators, and worker join. Use installer gather commands before destroying evidence.

    ### Production caution

    Do not select UPI only because it feels more customizable. Ensure the team can operate machine replacement and infrastructure lifecycle afterward.

---

## Q2. What DNS and load-balancer records are essential?

    ### L3 answer

    The Kubernetes API and internal API names must resolve correctly, and wildcard application DNS must point to ingress. Typical names are `api.<cluster>.<domain>`, `api-int.<cluster>.<domain>`, and `*.apps.<cluster>.<domain>`. Load balancers must route the documented ports to healthy bootstrap, control-plane, or ingress backends at the correct installation stage.

Split-horizon DNS, reverse lookup, TTL, firewall, and health-check behavior must be validated from the node networks, not only an administrator laptop.

    ### How to prove it

    Use `dig` or `getent hosts` for API, internal API, and a test apps name. Test API readiness with `curl -k https://api...:6443/readyz` and console/route access through the ingress endpoint.

    ### Troubleshooting / operational approach

    If bootstrap stalls, verify API and machine-config-server paths, backend health checks, and whether the load balancer still points to bootstrap when needed. If only routes fail, focus on wildcard DNS and ingress.

    ### Production caution

    Never use a generic TCP health check when the documented endpoint requires a specific health path or protocol.

---

## Q3. Explain the bootstrap process.

    ### L3 answer

    The bootstrap node temporarily hosts services that help control-plane machines form the permanent cluster. Control-plane nodes fetch ignition, start RHCOS, kubelet, CRI-O, and static control-plane pods. Operators then converge until the installer confirms the permanent control plane can operate without bootstrap.

A bootstrap timeout is a symptom. Common causes are DNS, load balancer, firewall, time, ignition, certificate, release image, or static-pod failure.

    ### How to prove it

    Use `openshift-install wait-for bootstrap-complete --log-level=debug`, installer gather commands, bootstrap journals, kubelet and CRI-O logs, and control-plane connectivity tests.

    ### Troubleshooting / operational approach

    Find the first component that did not become healthy. Correlate installer output with host logs and load-balancer/DNS evidence.

    ### Production caution

    Do not repeatedly rebuild without preserving logs; doing so hides a reproducible infrastructure defect.

---

## Q4. What belongs in an enterprise installation readiness checklist?

    ### L3 answer

    Include supported platform and release, sizing, failure domains, DNS, NTP, load balancers, firewall, proxy and noProxy, MTU, IP planning, pull secret, registry access or mirrored content, certificates, identity integration, storage, backup, monitoring, security baseline, capacity, support entitlement, and operational ownership.

Also define RPO/RTO, update channel, Operator compatibility, namespace onboarding, GitOps boundaries, maintenance, incident contacts, and acceptance criteria.

    ### How to prove it

    Use a signed readiness document with validation output for DNS, time, ports, registry, storage, capacity, and failure-domain design.

    ### Troubleshooting / operational approach

    Block installation if a critical prerequisite is unowned or untested. Resolve exceptions through a formal risk process.

    ### Production caution

    A successful installer exit does not prove production readiness.

---

## Q5. How do you troubleshoot release image pull failure?

    ### L3 answer

    Separate DNS, routing, proxy, TLS trust, authentication, mirror mapping, missing digest, and rate limiting. Validate from bootstrap and control-plane nodes because the bastion may have different network access.

In disconnected environments, confirm the release digest and every referenced image exist in the mirror, the mirror CA is trusted, and the mirror configuration maps the source correctly.

    ### How to prove it

    Use `oc adm release info`, `podman login`, `podman pull`, `curl -v https://registry/v2/`, and CRI-O logs on the affected node.

    ### Troubleshooting / operational approach

    Read the exact registry response. Compare a healthy and failed node, including proxy, CA bundle, time, DNS, and rendered container configuration.

    ### Production caution

    Do not disable TLS verification to work around corporate interception. Install the approved CA through supported configuration.

---

## Q6. What is Ignition and how is it used?

    ### L3 answer

    Ignition is a first-boot provisioning system used by RHCOS. It applies declarative storage, files, users, and systemd configuration early in boot. The installer generates bootstrap, control-plane, and worker ignition configurations.

Ignition is not the normal day-2 configuration tool. Persistent node changes after installation belong in MCO-managed resources.

    ### How to prove it

    Check ignition fetch and first-boot logs, served file content type, URL reachability, file hashes, host time, and whether credentials have expired for delayed provisioning.

    ### Troubleshooting / operational approach

    If a node fails before registering, follow boot and ignition evidence first. If it registers but does not converge, move to kubelet, CSR, CNI, and MachineConfig troubleshooting.

    ### Production caution

    Do not reuse ignition files across different clusters or assume an old generated file remains valid indefinitely.

---

## Q7. How do you validate a new cluster before handover?

    ### L3 answer

    Validate ClusterVersion, Operators, nodes, MachineConfigPools, CSRs, DNS, ingress, registry, image pull, scheduling, Service connectivity, persistent storage, expansion or snapshots where required, OAuth, RBAC, SCC, monitoring, alerts, and backup.

Run a canary application across failure domains, expose it, write to storage, restart it, and test a controlled drain. Document versions, infrastructure, certificates, mirror/proxy, storage classes, owners, and exceptions.

    ### How to prove it

    Produce an acceptance report containing command output, screenshots where appropriate, canary results, backup checksum, and signed owner approval.

    ### Troubleshooting / operational approach

    Any failed acceptance item must have a documented owner, risk, workaround, and closure date before production onboarding.

    ### Production caution

    Do not hand over a cluster with unexplained Degraded or Progressing conditions.

---

## Q8. Why does a node fail to join?

    ### L3 answer

    Typical causes are ignition failure, expired bootstrap credentials, DNS or routing problems, blocked API or machine-config-server ports, time drift, image pull failure, pending CSRs, duplicate hostname, unsupported hardware, kubelet/CRI-O failure, or CNI initialization errors.

Trace stages: boot, ignition, API reachability, kubelet, CSR, node object, CNI, MachineConfig, and Ready.

    ### How to prove it

    Use `oc get csr`, `oc describe csr`, `oc get nodes`, node journal, API readiness tests, DNS, routes, and CRI-O logs.

    ### Troubleshooting / operational approach

    Locate the first missing stage. Validate CSR identity and expected host before approval.

    ### Production caution

    Never approve all CSRs blindly. Unexpected CSRs can indicate a security or provisioning problem.

---

## Q9. Explain connected, restricted, and disconnected clusters.

    ### L3 answer

    Connected clusters reach required external services directly or through an approved proxy. Restricted clusters use controlled egress and internal mirrors. Fully disconnected clusters depend on externally mirrored releases, Operator catalogs, application images, internal DNS/NTP, and offline support workflows.

Disconnected operation changes installation, updates, vulnerability data, signatures, support data transfer, and image lifecycle.

    ### How to prove it

    Inventory every external dependency and prove the internal mirror contains release, catalog, bundle, operand, and application content needed for the full upgrade path.

    ### Troubleshooting / operational approach

    Treat the mirror as production infrastructure with HA, backup, capacity, TLS, monitoring, and retention. Test update and Operator resolution offline.

    ### Production caution

    Mirroring only the current image set is insufficient if the next upgrade graph or dependency is absent.

---

## Q10. How would you design a multi-zone production cluster?

    ### L3 answer

    Spread control-plane nodes across three independent zones with low latency. Distribute workers so one zone loss leaves enough capacity for critical workloads. Use topology spread, anti-affinity, topology-aware storage, resilient load balancers, and DNS independent of one zone.

Replica counts, PDBs, storage classes, and autoscaling minimums must support both planned maintenance and zone failure.

    ### How to prove it

    Model one-zone failure, check remaining allocatable capacity, storage attachability, ingress backends, and control-plane quorum. Run controlled failure tests in a non-production environment.

    ### Troubleshooting / operational approach

    Correct placement, topology, or capacity before onboarding critical workloads. A cluster that only survives in steady state is not highly available.

    ### Production caution

    Do not span high-latency data centers as one control plane without validating Red Hat guidance and etcd latency requirements.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/installation_overview/ocp-installation-overview
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/architecture/control-plane
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/release_notes/ocp-4-21-release-notes

---

<!-- Original file: 03. Operators and OLM.md -->

# OpenShift Administrator L3 Interview Q&A — Operators and OLM

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** OLM resources, catalogs, upgrades, CSV failures, webhooks, and operands.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain CatalogSource, Subscription, InstallPlan, CSV, and OperatorGroup.

    ### L3 answer

    CatalogSource exposes Operator packages and bundles. Subscription selects a package, channel, source, namespace, and approval strategy. OLM resolves dependencies and creates an InstallPlan. The InstallPlan installs or upgrades a ClusterServiceVersion. The CSV describes the Operator version, permissions, owned/required CRDs, deployment strategy, and lifecycle status. OperatorGroup controls which namespaces the Operator watches.

The resource chain is the primary troubleshooting map.

    ### How to prove it

    Run `oc get catalogsource,subscription,installplan,csv,operatorgroup -A` and describe the object where status first stops progressing.

    ### Troubleshooting / operational approach

    Check catalog availability, package/channel, dependency resolution, approval, bundle unpacking, CSV requirements, deployment, CRDs, API services, webhooks, and operand.

    ### Production caution

    Do not delete the complete chain as a first action. You may remove evidence and create duplicate ownership or partial uninstall.

---

## Q2. Why can an Operator upgrade remain Pending?

    ### L3 answer

    Pending upgrades commonly result from manual approval, unavailable catalog content, dependency conflicts, unsupported upgrade edges, a failed prior CSV, RBAC problems, webhook errors, CRD ownership conflict, or OperatorGroup mismatch.

A channel may move to a newer bundle even if the Subscription did not change, so production promotion must be governed.

    ### How to prove it

    Inspect Subscription conditions and referenced InstallPlan, then CSV requirements and events. Check catalog pod health and bundle unpack jobs.

    ### Troubleshooting / operational approach

    Resolve the exact requirement. For manual approval, review compatibility and change records before approving.

    ### Production caution

    Do not approve simply to clear Pending. A schema or webhook change can affect the entire cluster.

---

## Q3. How do Automatic and Manual InstallPlan approval differ?

    ### L3 answer

    Automatic approval lets OLM proceed when a new eligible plan is resolved. Manual approval creates a human or automation gate for each plan. Manual approval is common in regulated production, but it is not a substitute for testing.

The team must review supported OpenShift versions, CRD and webhook changes, resource requirements, operand behavior, and rollback limitations.

    ### How to prove it

    Read the Subscription `installPlanApproval`, inspect the generated InstallPlan content, and record the target CSV and dependencies.

    ### Troubleshooting / operational approach

    Use automatic in controlled lower environments if appropriate, then promote known content to production with manual review.

    ### Production caution

    A downgrade may be unsupported after CRD or data-schema migration, so “we can roll back the CSV” is not a valid plan by itself.

---

## Q4. How do you troubleshoot a Failed CSV?

    ### L3 answer

    Start with CSV status reason/message and unmet requirements. Check its Operator deployment, service account, roles, CRDs, API services, webhooks, and namespace events. In disconnected environments, verify bundle and operand images exist.

`Pending` often means requirements are not met; `Failed` can indicate installation strategy, deployment, API, permission, or ownership errors.

    ### How to prove it

    Use `oc describe csv`, `oc get deploy,pod,sa,role,rolebinding`, `oc get crd`, `oc get apiservices`, and events.

    ### Troubleshooting / operational approach

    Fix the first failed dependency, then let OLM reconcile. Confirm the Operator deployment and a real operand transaction.

    ### Production caution

    Deleting a CRD deletes all custom resources of that type. Never use CRD deletion to clear ownership conflict casually.

---

## Q5. How do you safely upgrade an add-on Operator?

    ### L3 answer

    Verify the vendor compatibility matrix, supported upgrade edge, release notes, CRD changes, and workload impact. Back up operand data and custom resources. Test the exact source, channel, and OpenShift version in a representative environment.

In production, confirm current Operator and operand health, approve the reviewed plan, monitor CSV and deployments, then validate managed services.

    ### How to prove it

    Capture Subscription, InstallPlan, CSV, CRs, CRDs, and operand health before and after. Record version and channel.

    ### Troubleshooting / operational approach

    Stop if the CSV fails, webhooks become unavailable, or operand health regresses. Follow vendor recovery guidance rather than attempting an unsupported downgrade.

    ### Production caution

    Operator pod health alone does not prove the managed database, storage, or security service is healthy.

---

## Q6. What causes CRD ownership conflicts?

    ### L3 answer

    OLM tracks which CSV owns a CRD. Conflicts occur when multiple installed Operators claim the same CRD, duplicate installations exist, or migration between Operators is incomplete.

Ownership must be transferred through a supported upgrade or migration path while preserving custom resources.

    ### How to prove it

    Find all CSVs that list the CRD, all OperatorGroups, and historical namespace installations. Inventory CR instances and backups.

    ### Troubleshooting / operational approach

    Determine the authoritative Operator, follow vendor cleanup or migration steps, and validate conversion webhooks and stored versions.

    ### Production caution

    Never delete the CRD to solve ownership unless all custom resources can be permanently lost or restored.

---

## Q7. What is an operand?

    ### L3 answer

    The Operator is the controller; the operand is the managed application or service. A healthy Operator can manage an unhealthy operand, and an operand may keep running temporarily while its Operator is down.

Troubleshooting must identify whether the management plane, data plane, or both are affected.

    ### How to prove it

    Check CSV and Operator deployment, then the custom resource status, operand pods, services, storage, certificates, alerts, and functional transaction.

    ### Troubleshooting / operational approach

    If reconciliation is healthy but the operand fails, inspect desired configuration, external dependencies, capacity, and application-specific logs.

    ### Production caution

    Do not declare success based only on `CSV Succeeded`.

---

## Q8. How do you manage a custom catalog in a disconnected cluster?

    ### L3 answer

    Mirror the approved file-based or index catalog and every referenced bundle and operand image to an internal registry. Create a CatalogSource for the mirrored content and configure image mirrors and trust.

Catalog promotion should follow software release management: build, test, approve, publish immutable content, retain prior versions, and prune deliberately.

    ### How to prove it

    Verify CatalogSource READY status, package visibility, bundle unpacking, image availability, TLS trust, and complete upgrade graph.

    ### Troubleshooting / operational approach

    If resolution fails, compare catalog contents with the required package/channel and inspect catalog and OLM logs.

    ### Production caution

    A partial mirror may install today and fail during the next dependency or upgrade.

---

## Q9. How do Operator webhook failures affect a cluster?

    ### L3 answer

    Admission and conversion webhooks can block resource creation or updates when their Service has no endpoints, certificates fail, DNS or networking is broken, or the webhook is misconfigured. If failure policy is `Fail`, the blast radius may extend beyond the Operator namespace.

Conversion webhook failure can make stored custom resources unreadable at another API version.

    ### How to prove it

    Inspect validating/mutating webhook configurations, service endpoints, pod readiness, CA bundles, certificates, API errors, and APIService status.

    ### Troubleshooting / operational approach

    Restore the webhook service and certificate chain. Use emergency bypass only with vendor guidance and a risk assessment.

    ### Production caution

    Deleting or disabling a webhook can admit invalid state and make later reconciliation or upgrades worse.

---

## Q10. How do you prove an Operator is healthy?

    ### L3 answer

    Prove CatalogSource and Subscription resolution, InstallPlan completion, CSV `Succeeded`, Operator deployment readiness, absence of repeated restarts, webhook/APIService availability, successful reconciliation, and operand functionality.

Also verify no critical alerts, storage issues, or pending migrations remain.

    ### How to prove it

    Collect OLM resources, deployment status, logs, custom-resource conditions, managed service endpoints, and a functional test.

    ### Troubleshooting / operational approach

    Compare desired and observed generations, reconcile timestamps, and operand status. Document installed version, source, channel, scope, and owner.

    ### Production caution

    No log errors is not evidence of successful reconciliation; use resource conditions and a real service test.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/operators/index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/release_notes/ocp-4-21-release-notes

---

<!-- Original file: 04. Node Machine and Scheduling Administration.md -->

# OpenShift Administrator L3 Interview Q&A — Node, Machine, and Scheduling Administration

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Machine API, MCO, node lifecycle, scheduling, resources, disruption, and autoscaling.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain Machine, MachineSet, Node, and MachineConfigPool.

    ### L3 answer

    A Machine represents infrastructure intended to become a Kubernetes node. A MachineSet maintains a desired number of similar Machines. A Node is the Kubernetes registration of the host after kubelet joins. A MachineConfigPool groups nodes by role and coordinates rendered MachineConfig rollout.

On supported platforms, Machine API objects may create, replace, and delete infrastructure. On UPI platforms, a Node may not have a fully managed Machine behind it.

    ### How to prove it

    Use `oc get machines,machinesets -n openshift-machine-api`, `oc get nodes`, `oc get mcp`, and compare Machine provider ID with Node provider ID.

    ### Troubleshooting / operational approach

    Determine which layer owns lifecycle before deletion. For OS convergence problems, inspect the pool and node annotations. For infrastructure failure, inspect the Machine and cloud/platform events.

    ### Production caution

    Deleting a Node object is not the same as deleting a VM. Deleting a Machine may destroy infrastructure and attached ephemeral data.

---

## Q2. How do you troubleshoot a NotReady node?

    ### L3 answer

    First determine whether one node or many are affected and identify the exact Node condition. Common causes include kubelet or CRI-O failure, API connectivity, certificate problems, CNI failure, disk/inode pressure, memory or PID pressure, kernel faults, and infrastructure networking.

Cordon the node when needed to prevent new placement. Preserve workload and host evidence before rebooting.

    ### How to prove it

    Use `oc describe node`, list all pods on the node, inspect MCO state, then `oc debug node/<node>` and check `systemctl status kubelet crio`, journals, filesystems, memory, routes, and interfaces.

    ### Troubleshooting / operational approach

    Follow the condition and timestamp. If kubelet stopped reporting, test node-to-API connectivity and journals. If Ready is false after CNI errors, inspect OVN pods and node networking. If pressure exists, identify the consuming path.

    ### Production caution

    Do not drain or reboot before checking PDBs, local storage, replicas, and available capacity on other nodes.

---

## Q3. Explain cordon, drain, and uncordon.

    ### L3 answer

    Cordon marks a node unschedulable for new pods. Drain evicts eligible workload pods for maintenance. Uncordon returns the node to scheduling.

A production drain is a controlled application-disruption action. It must account for PDBs, static pods, DaemonSets, local data, StatefulSets, volume detach, affinity, and remaining capacity.

    ### How to prove it

    Use `oc adm cordon`, review `oc get pdb -A`, list node pods, then run a reviewed drain command. After maintenance, validate node, MCO, CNI, CSI, kubelet, and CRI-O before `oc adm uncordon`.

    ### Troubleshooting / operational approach

    If drain is blocked, fix redundancy, scale the application, or coordinate a PDB change. Record any forced eviction.

    ### Production caution

    Do not use `--force` or delete emptyDir data by habit. Those flags can cause outage or data loss.

---

## Q4. What does a degraded MachineConfigPool mean?

    ### L3 answer

    A degraded pool means one or more nodes cannot converge to the rendered machine configuration or the MCO reports rollout failure. Causes include invalid configuration, failed drain, unavailable node, disk full, kubelet/CRI-O failure, conflicting files, or a node stuck on an old rendered config.

The rendered MachineConfig is generated from source MachineConfigs and other supported configuration objects.

    ### How to prove it

    Run `oc describe mcp <pool>`, compare updated/ready/degraded counts, inspect node currentConfig/desiredConfig/state/reason annotations, and read Machine Config Daemon logs.

    ### Troubleshooting / operational approach

    Identify whether failure is rendering, draining, applying, rebooting, or returning Ready. Correct or remove the source MachineConfig to produce a new valid rendered config.

    ### Production caution

    Never edit rendered MachineConfigs or fake node annotations to mark a node updated.

---

## Q5. How do labels, affinity, taints, and tolerations work together?

    ### L3 answer

    Labels describe node attributes. Node selectors and node affinity attract or require workloads to matching nodes. Taints repel pods; tolerations permit a pod to remain eligible but do not force it onto that node.

Dedicated infrastructure nodes normally use both a label/affinity rule and a taint/toleration so intended workloads are placed there and general workloads are repelled.

    ### How to prove it

    Inspect node labels and taints with `oc describe node` and pod selectors, affinity, and tolerations in YAML. Read scheduler events for the exact mismatch.

    ### Troubleshooting / operational approach

    Remove one constraint at a time in a test environment or correct the workload/node design. Verify topology and capacity after placement.

    ### Production caution

    Do not remove health-related or role taints just to make a pod schedule; fix the underlying condition.

---

## Q6. Explain requests, limits, QoS, throttling, OOM, and eviction.

    ### L3 answer

    The scheduler places pods based primarily on requests. Limits constrain runtime consumption. Kubernetes derives Guaranteed, Burstable, or BestEffort QoS from resource configuration.

CPU limit pressure causes throttling. A container exceeding its memory cgroup limit can be OOMKilled even if the node has free memory. Node pressure can trigger kubelet eviction based on signals, priority, QoS, and usage.

    ### How to prove it

    Use pod describe output, termination state, `oc adm top`, node conditions, events, and host kernel logs. Compare requests/limits with actual working set and application configuration.

    ### Troubleshooting / operational approach

    Classify the event as scheduling, cgroup OOM, node OOM, eviction, or CPU throttling. Correct sizing, leaks, capacity, or workload design accordingly.

    ### Production caution

    Increasing limits without capacity analysis may move a container OOM into a node-wide outage.

---

## Q7. What is a PodDisruptionBudget and what does it not do?

    ### L3 answer

    A PDB limits voluntary disruption using `minAvailable` or `maxUnavailable`. It helps during drains, updates, and administrative evictions.

It does not prevent involuntary host failure and does not create replicas. A PDB on a single-replica application can block maintenance indefinitely while still providing no protection from node loss.

    ### How to prove it

    Run `oc get pdb -A` and describe the relevant PDB. Compare desired healthy pods, current healthy pods, disruptions allowed, replica count, placement, and readiness.

    ### Troubleshooting / operational approach

    Scale or redistribute the workload before maintenance. Change a PDB only with application-owner approval and documented risk.

    ### Production caution

    Do not delete a PDB merely because a drain is urgent unless the business accepts the resulting availability risk.

---

## Q8. How do you troubleshoot Pending pods?

    ### L3 answer

    A pod can remain Pending because it is unschedulable or because storage binding is incomplete. Scheduler messages commonly identify insufficient resources, taints, affinity, topology constraints, unbound PVCs, or unavailable extended resources.

Cluster-wide free CPU does not prove the request fits on one eligible node.

    ### How to prove it

    Use `oc describe pod`, events, node labels/taints, quotas, PVCs, StorageClasses, and scheduler messages.

    ### Troubleshooting / operational approach

    Separate `PodScheduled=False` from a scheduled pod stuck during initialization. Fix the exact placement or storage constraint rather than adding random capacity.

    ### Production caution

    Do not reduce requests solely to force scheduling if the application actually needs the resources.

---

## Q9. How do you safely replace a failed worker?

    ### L3 answer

    Confirm lifecycle ownership. On Machine API-managed infrastructure, replace through the Machine or MachineSet. If reachable, cordon and drain after safety checks. Preserve diagnostics and verify storage detach and external resources.

The replacement must receive expected ignition, CSR approval, labels, taints, MachineConfig, CNI/CSI components, and monitoring.

    ### How to prove it

    Capture Machine, Node, provider status, pods, volume attachments, and MCO state. After replacement, verify Ready, pool convergence, version, daemon pods, and a test workload.

    ### Troubleshooting / operational approach

    Remove stale objects only after the new node is validated and external volume/network cleanup is complete.

    ### Production caution

    Do not clone an existing RHCOS VM manually; duplicate identity and certificates can corrupt registration and Machine API state.

---

## Q10. How does cluster autoscaling interact with scheduling?

    ### L3 answer

    The Cluster Autoscaler adds or removes machine capacity according to unschedulable pods and configured limits, while MachineAutoscalers target machine sets. It cannot solve invalid affinity, missing storage topology, quota, or unsupported device requests unless a matching node type is available.

Scale-down considers pod mobility, PDBs, utilization, and special constraints.

    ### How to prove it

    Check autoscaler configuration, MachineAutoscalers, unschedulable pod events, machine-set health, and whether a node type can satisfy every scheduling predicate.

    ### Troubleshooting / operational approach

    Set minimum capacity for zone failure, platform workloads, updates, and bursts. Tune scale-down only after verifying application disruption behavior.

    ### Production caution

    Autoscaling to zero or very low minimums can remove failure tolerance and make recovery slower than the business RTO.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/machine_management/overview-of-machine-management
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/machine_configuration/machine-config-index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/nodes/working-with-pods

---

<!-- Original file: 05. Networking DNS Ingress and Network Security.md -->

# OpenShift Administrator L3 Interview Q&A — Networking, DNS, Ingress, and Network Security

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** OVN-Kubernetes, Services, Routes, DNS, policy, MTU, egress, and secondary networks.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain the external request path to a pod.

    ### L3 answer

    A client resolves the application hostname to a load balancer. The load balancer forwards to router pods managed by the Ingress Operator. The router matches a Route or Ingress, selects a Service, and forwards to a ready pod endpoint over the cluster network.

DNS, load balancer, IngressController, router, Route, Service, EndpointSlice, NetworkPolicy, OVN, and application readiness are separate failure points.

    ### How to prove it

    Use `dig`, `curl -vk`, `oc get route -o yaml`, `oc get svc,endpointslices,pod -o wide`, router pod status/logs, and IngressController conditions.

    ### Troubleshooting / operational approach

    Test each hop. If direct pod access fails, fix application or pod network. If pod works but Service fails, inspect selectors/endpoints/service programming. If Service works but Route fails, inspect ingress and TLS.

    ### Production caution

    A router 503 and an application-generated 503 are different; identify the response source before remediation.

---

## Q2. What is OVN-Kubernetes?

    ### L3 answer

    OVN-Kubernetes is the default OpenShift network provider in current releases. It uses logical switches, routers, ACLs, distributed routing, and Open vSwitch/OVN components to implement pod networking, Services, policy, egress features, and related functions.

Troubleshooting should establish whether the fault is node-local, namespace policy, Service programming, control component, or underlay network.

    ### How to prove it

    Run `oc get co network`, inspect pods in `openshift-ovn-kubernetes`, network configuration, node status, and network alerts. Compare affected and healthy nodes.

    ### Troubleshooting / operational approach

    Map source, destination, node, protocol, and time. Check MTU, tunnel/underlay reachability, interface drops, policy, and component logs.

    ### Production caution

    Do not restart all OVN pods or delete OVN databases simultaneously. Preserve the topology pattern and logs.

---

## Q3. Differentiate Service, Route, Ingress, NodePort, and LoadBalancer.

    ### L3 answer

    A Service provides stable virtual addressing to selected pods. ClusterIP is internal, NodePort opens a port on nodes, and LoadBalancer asks supported infrastructure to create external access. Kubernetes Ingress defines HTTP(S) routing for an ingress controller. OpenShift Route is the native external HTTP(S) resource with edge, passthrough, and re-encrypt TLS options.

The correct choice depends on protocol, security, platform integration, and ownership.

    ### How to prove it

    Inspect the resource type, controller status, allocated address, Service endpoints, and external load-balancer or DNS configuration.

    ### Troubleshooting / operational approach

    Use Route or Ingress for HTTP(S), LoadBalancer where supported for external L4 services, and NodePort only when the architecture requires it.

    ### Production caution

    Do not expose every application through NodePort; it increases port management and security complexity.

---

## Q4. How do you troubleshoot a Service when pods are healthy?

    ### L3 answer

    Confirm the Service selector matches pod labels, EndpointSlices contain ready addresses, targetPort maps to the actual listener, and the process listens on the pod IP rather than only localhost. Then evaluate NetworkPolicy, OVN service programming, and node scope.

Readiness controls whether endpoints receive traffic.

    ### How to prove it

    Use `oc get svc -o yaml`, `oc get endpointslices`, pod labels, `ss -lntp` inside the pod, and connectivity tests from the same and another namespace.

    ### Troubleshooting / operational approach

    Compare direct pod IP with Service ClusterIP. If pod works but Service does not, focus on selectors, endpoints, targetPort, policy, or service programming.

    ### Production caution

    Do not change the Service selector until you prove the intended pod labels and rollout behavior.

---

## Q5. Explain default-deny NetworkPolicy.

    ### L3 answer

    NetworkPolicy is additive. When a pod is selected for a direction, allowed traffic is the union of matching policies. A default-deny policy selects pods but allows no ingress and/or egress until explicit allow policies are added.

A corporate baseline normally includes explicit DNS, ingress, monitoring, shared services, and required external dependencies.

    ### How to prove it

    List all policies, selected pod labels, namespace labels, ports, and directions. Test from controlled source pods and review flow observability where available.

    ### Troubleshooting / operational approach

    Stage policy in lower environments, establish required flows, then enforce. Keep platform and tenant responsibilities clear.

    ### Production caution

    A broad egress deny can break DNS, image access, identity, monitoring, storage, and external APIs.

---

## Q6. What is AdminNetworkPolicy?

    ### L3 answer

    AdminNetworkPolicy is cluster-scoped and allows administrators to enforce controls that tenant NetworkPolicies cannot override. It is suitable for mandatory multi-tenant isolation and organization-wide ingress or egress rules.

Its priority and broad scope create a high blast radius.

    ### How to prove it

    Inspect policy priority, subjects, peers, rule order, namespace labels, and affected flows. Validate against representative platform and tenant traffic.

    ### Troubleshooting / operational approach

    Deploy through reviewed policy-as-code, stage changes, and maintain explicit exceptions. Use network observability to confirm impact.

    ### Production caution

    Do not apply a cluster-wide deny without testing DNS, ingress, monitoring, storage, authentication, and support access.

---

## Q7. How do you troubleshoot DNS?

    ### L3 answer

    First classify pod-to-Service DNS, external recursion, one-node failure, or cluster-wide failure. Check the DNS Operator, DNS pods, Service/endpoints, upstream resolvers, node network, and NetworkPolicies.

Test exact short and fully qualified names and inspect response code, latency, search domains, and `ndots` behavior.

    ### How to prove it

    Use `oc get co dns`, inspect `openshift-dns` pods, pod `/etc/resolv.conf`, `getent hosts`, `dig`, and compare affected/healthy nodes.

    ### Troubleshooting / operational approach

    If only one node fails, inspect the DNS daemon pod and node CNI path. If external names fail cluster-wide but Services resolve, inspect upstream forwarders and egress policy.

    ### Production caution

    Ping is not a DNS diagnostic. Successful resolution does not prove application connectivity.

---

## Q8. Why does a Route return 503?

    ### L3 answer

    Common causes are no usable endpoints, unready pods, wrong Service port or targetPort, route-to-service mismatch, backend protocol mismatch, or TLS errors. Re-encrypt also depends on destination CA validation; passthrough requires the backend to terminate TLS.

The router may be healthy while the application has no ready backend.

    ### How to prove it

    Inspect Route admission, Service, EndpointSlices, readiness, router logs, and direct backend connectivity. Use `curl -vk` and note headers and TLS stage.

    ### Troubleshooting / operational approach

    Fix readiness, selector, port, or TLS configuration at the failing layer. Revalidate through the external hostname.

    ### Production caution

    Do not restart router pods before checking that the Service actually has ready endpoints.

---

## Q9. How does MTU mismatch present?

    ### L3 answer

    MTU mismatch may allow small packets but break larger payloads, TLS handshakes, image pulls, or cross-node streams. Symptoms include stalls, retransmissions, intermittent timeouts, and failures tied to one path or node.

Overlay networking reduces effective payload MTU, so underlay and cluster configuration must align.

    ### How to prove it

    Inspect cluster network MTU, physical and virtual interface MTUs, VPN/cloud path, and use `tracepath` or controlled do-not-fragment ping tests where supported.

    ### Troubleshooting / operational approach

    Compare same-node and cross-node communication and small versus large payloads. Plan any cluster MTU migration through supported procedures.

    ### Production caution

    Do not manually change MTU on one RHCOS node; it creates drift and asymmetric behavior.

---

## Q10. How do you troubleshoot intermittent cross-node communication?

    ### L3 answer

    Map failures by source node, destination node, protocol, namespace, and time. Compare same-node traffic, cross-node traffic, and node pairs. Check OVN components, underlay reachability, interface drops, MTU, conntrack pressure, NIC or switch errors, and policy.

Intermittent topology patterns are valuable evidence.

    ### How to prove it

    Run repeated controlled probes, inspect node and OVN metrics/logs, interface counters, alerts, and packet captures where approved.

    ### Troubleshooting / operational approach

    If one node pair fails, focus on underlay/tunnel path. If one namespace fails, focus on policy and endpoints. If failures are load-related, inspect drops and capacity.

    ### Production caution

    Mass restarts can remove the exact node-pair pattern needed for root cause.

---

## Q11. Explain egress IP, EgressFirewall, and egress router.

    ### L3 answer

    Egress IP gives selected namespace traffic a predictable source IP. EgressFirewall restricts outbound destinations. An egress router provides a specialized routing path through defined networking.

Choose based on source identity, destination control, or routing requirement. Consider failover, node placement, external firewall state, DNS-based rules, and observability.

    ### How to prove it

    Inspect egress resources, namespace selectors, node eligibility, assigned addresses, network policy, and external firewall logs.

    ### Troubleshooting / operational approach

    Test from selected and unselected pods, fail the assigned node in a controlled environment, and confirm source address and recovery.

    ### Production caution

    Source IP allowlisting should not replace TLS identity and application authorization.

---

## Q12. What are Multus and secondary networks?

    ### L3 answer

    Multus attaches one or more secondary interfaces to a pod in addition to the default pod network. NetworkAttachmentDefinition resources describe the secondary CNI. Use cases include storage, telecom, SR-IOV, VLAN, high-performance, and isolated legacy networks.

A pod may be Running while its secondary interface is unusable.

    ### How to prove it

    Inspect the pod network annotation/status, NetworkAttachmentDefinition, node device availability, IPAM, interface, route, VLAN, MTU, and CNI logs.

    ### Troubleshooting / operational approach

    Classify default-network versus secondary-network failure. Verify IP allocation, underlay switch configuration, and application binding.

    ### Production caution

    Secondary networks expand security and operational ownership. Define IPAM, policy, monitoring, and support before production use.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/networking_overview/networking_overview
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/ovn-kubernetes_network_plugin/about-ovn-kubernetes
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/network_security/network-policy

---

<!-- Original file: 06. Storage CSI and Stateful Workloads.md -->

# OpenShift Administrator L3 Interview Q&A — Storage, CSI, and Stateful Workloads

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** PV/PVC, StorageClass, topology, snapshots, expansion, attachment, and production design.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain PV, PVC, StorageClass, CSI, and dynamic provisioning.

    ### L3 answer

    A PersistentVolume represents storage. A PersistentVolumeClaim is a namespace-scoped request for capacity, access mode, and storage attributes. A StorageClass defines a provisioning profile, including driver, parameters, binding mode, reclaim policy, and expansion support. CSI controller and node components connect Kubernetes to the backend.

Dynamic provisioning creates a backend volume and PV in response to a PVC.

    ### How to prove it

    Use `oc get sc,pv`, `oc get pvc -A`, describe the PVC/PV, inspect CSI Operator/controller/node pods, and verify backend state.

    ### Troubleshooting / operational approach

    Trace provisioning, binding, attachment, node staging, mount, filesystem, security context, and application access. The first failed stage identifies the responsible component.

    ### Production caution

    Do not create arbitrary static PVs to hide a failed CSI provisioner.

---

## Q2. How do you troubleshoot a PVC stuck Pending?

    ### L3 answer

    Check requested StorageClass, default class, access mode, capacity, selector, volume mode, topology, events, backend capacity, credentials, and CSI controller health. With `WaitForFirstConsumer`, Pending can be normal until a consuming pod is scheduled.

A pod scheduling constraint and storage topology can interact.

    ### How to prove it

    Use `oc describe pvc`, `oc get sc -o yaml`, `oc get pv`, pod events, CSI controller logs, and backend quota/capacity.

    ### Troubleshooting / operational approach

    Read the provisioner error. If the class waits for a consumer, inspect pod scheduling and allowed topology. If provisioning fails, fix backend, credentials, network, or parameters.

    ### Production caution

    Do not manually bind a mismatched PV; access mode, class, size, topology, and volume mode must align.

---

## Q3. Immediate versus WaitForFirstConsumer?

    ### L3 answer

    Immediate provisioning occurs as soon as the PVC is created. In zonal storage, this can select a zone before pod scheduling. WaitForFirstConsumer delays binding/provisioning until a pod is considered so the scheduler and CSI provisioner can coordinate topology.

The latter often prevents zone mismatch but makes a PVC remain Pending until a pod exists.

    ### How to prove it

    Inspect StorageClass `volumeBindingMode`, pod scheduling messages, selected-node annotations, node zone labels, and allowed topologies.

    ### Troubleshooting / operational approach

    Resolve placement and topology together. Confirm the selected node can access the provisioned volume.

    ### Production caution

    Changing binding mode on an existing StorageClass does not retroactively move existing volumes.

---

## Q4. Explain RWO, ROX, RWX, and RWOP.

    ### L3 answer

    ReadWriteOnce generally allows read-write mounting from one node, not necessarily one pod. ReadOnlyMany allows read-only use from many nodes. ReadWriteMany allows read-write use from many nodes when the backend supports it. ReadWriteOncePod restricts read-write use to one pod where supported.

The access mode request cannot create a capability the driver or backend lacks.

    ### How to prove it

    Check PVC/PV access modes, CSI support matrix, attachment objects, pod placement, and backend mount behavior.

    ### Troubleshooting / operational approach

    Choose access mode based on application consistency and failover. StatefulSets commonly use one RWO PVC per replica.

    ### Production caution

    Do not scale multiple replicas against one RWO claim and expect shared-write semantics.

---

## Q5. Explain Retain and Delete reclaim policies.

    ### L3 answer

    Delete normally removes the dynamically provisioned backend volume after the claim/PV lifecycle completes. Retain preserves the volume and data for manual recovery or cleanup.

Retain reduces accidental deletion risk but creates data-security and cleanup obligations. Delete is convenient but increases the impact of mistaken PVC deletion.

    ### How to prove it

    Inspect StorageClass and PV `persistentVolumeReclaimPolicy`, finalizers, claimRef, and backend resource after deletion tests.

    ### Troubleshooting / operational approach

    Combine reclaim policy with snapshots, application-consistent backup, RBAC, and restore testing.

    ### Production caution

    Retain is not a backup. Delete should not be used for critical data without independent recovery controls.

---

## Q6. How do you expand a PVC?

    ### L3 answer

    Confirm `allowVolumeExpansion`, driver/backend support, filesystem behavior, and application impact. Increase the PVC requested size; do not edit PV capacity directly. Kubernetes and CSI coordinate controller and node expansion.

Some filesystems expand online; some workflows require pod restart or remount.

    ### How to prove it

    Inspect StorageClass, patch the PVC request, watch PVC conditions/events, verify backend capacity, and run `df -h` inside the pod.

    ### Troubleshooting / operational approach

    If controller expansion succeeds but filesystem does not, inspect CSI node plugin and filesystem-resize conditions. Restart only if documented.

    ### Production caution

    Kubernetes generally does not support shrinking. To reduce size, migrate to a new volume.

---

## Q7. Explain VolumeSnapshot resources.

    ### L3 answer

    VolumeSnapshot is the namespace-scoped request, VolumeSnapshotContent is the cluster-scoped object representing the backend snapshot, and VolumeSnapshotClass defines driver and deletion behavior.

Snapshots may be crash-consistent, not application-consistent. Databases often require quiescing or native backup coordination.

    ### How to prove it

    Inspect snapshot ready status, bound content, class, driver, backend snapshot, deletion policy, and a restore into a new PVC.

    ### Troubleshooting / operational approach

    Test create, retain/delete, restore, and application validation. Include hooks or database procedures for consistency.

    ### Production caution

    A snapshot that has never been restored is unproven backup evidence.

---

## Q8. How do you troubleshoot Multi-Attach?

    ### L3 answer

    Multi-Attach commonly occurs when an RWO volume remains attached to an old node while a replacement pod starts elsewhere. Causes include unclean node failure, slow detach, stale attachment state, fencing delay, or incorrect multi-replica design.

Data integrity requires proving the old node cannot still write before forced detach.

    ### How to prove it

    Inspect pod events, `oc get volumeattachments`, old pod/node state, CSI attacher logs, and backend attachment/fencing status.

    ### Troubleshooting / operational approach

    Recover or fence the old node, allow supported detach, then validate filesystem and application. Fix workload design if multiple replicas share one RWO PVC.

    ### Production caution

    Forced detach without fencing can cause simultaneous writers and corruption.

---

## Q9. Filesystem versus block volume mode?

    ### L3 answer

    Filesystem mode mounts a filesystem into the pod. Block mode presents a raw block device. Block mode is used by applications that manage their own storage layout and explicitly support raw devices.

Backup, expansion, security, multipath, and recovery differ between the modes.

    ### How to prove it

    Check PVC/PV `volumeMode`, device visibility inside the container, CSI driver support, permissions, and application certification.

    ### Troubleshooting / operational approach

    Use vendor-supported procedures for formatting or raw-device management. Test failover and recovery.

    ### Production caution

    Do not run normal filesystem tools against a raw block device unless the application design requires it.

---

## Q10. How do you design storage for a critical stateful workload?

    ### L3 answer

    Start with latency, IOPS, throughput, capacity, access mode, consistency, replication, zone behavior, encryption, backup, snapshot, and RPO/RTO requirements. Map these to a supported CSI driver and StorageClass.

Design node and zone loss, detach delay, backend outage, filesystem full, and restore. Combine topology-aware binding, application replication, anti-affinity, PDB, and independent backups.

    ### How to prove it

    Benchmark representative workloads, test failover and restore, validate storage alerts, and record backend and application recovery procedures.

    ### Troubleshooting / operational approach

    Choose based on sustained production characteristics and support, not a single synthetic IOPS number.

    ### Production caution

    Highly available storage does not automatically make a single-instance database highly available or consistent.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/storage/storage
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/backup_and_restore/backup-restore-overview

---

<!-- Original file: 07. Authentication RBAC SCC and Security.md -->

# OpenShift Administrator L3 Interview Q&A — Authentication, RBAC, SCC, and Security

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** OAuth, identities, RBAC, SCC, service accounts, secrets, encryption, certificates, and audit.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Explain authentication, authorization, and admission.

    ### L3 answer

    Authentication identifies the caller. Authorization decides whether that identity may perform the verb on the resource, primarily through RBAC. Admission runs after authentication and authorization but before persistence, applying defaults, mutation, validation, SCC, and webhooks.

A valid login with `Forbidden` is usually authorization. A permitted request rejected for pod security is admission.

    ### How to prove it

    Use the API response, `oc whoami`, `oc auth can-i`, audit logs, pod events, SCC checks, and webhook status.

    ### Troubleshooting / operational approach

    Classify the stage first. Fix identity-provider mapping, RBAC, SCC, or webhook at the correct layer.

    ### Production caution

    Do not grant cluster-admin to diagnose a narrower permission or admission problem.

---

## Q2. Role, ClusterRole, RoleBinding, and ClusterRoleBinding?

    ### L3 answer

    A Role defines permissions in one namespace. A ClusterRole is cluster-scoped and may contain cluster permissions or reusable namespace permissions. A RoleBinding grants a Role or ClusterRole inside one namespace. A ClusterRoleBinding grants a ClusterRole across the cluster.

Least privilege favors groups, narrow verbs/resources, and namespace bindings.

    ### How to prove it

    Use `oc auth can-i --as`, `oc auth can-i --list`, list bindings, and `oc adm policy who-can`. Inspect aggregated roles and group membership.

    ### Troubleshooting / operational approach

    Reproduce with impersonation, identify the missing or excessive permission, and update the correct group binding through change control.

    ### Production caution

    Direct user bindings and broad wildcard roles are hard to audit and revoke.

---

## Q3. What are Security Context Constraints?

    ### L3 answer

    SCCs control pod security settings such as UID, SELinux context, privilege, capabilities, host namespaces, host paths, seccomp, and volume types. A pod is admitted under an SCC available to its user or service account that validates and can generate required values.

A broad SCC materially increases host risk.

    ### How to prove it

    Read pod admission events, service account, pod securityContext, `oc get scc`, and `oc auth can-i use scc/...` for the service account.

    ### Troubleshooting / operational approach

    Modify the workload to fit a restrictive SCC or create a minimal custom SCC. Grant only to the dedicated service account.

    ### Production caution

    Do not grant `privileged` merely to make an image start. First correct UID, filesystem permissions, and capabilities.

---

## Q4. How do you troubleshoot an identity provider?

    ### L3 answer

    OpenShift OAuth integrates with supported identity providers. Failure can occur at provider reachability, TLS, callback, claim mapping, user/identity mapping, token issuance, OAuth route, or API token use.

A provider may authenticate the user successfully while claims or mapping create the wrong OpenShift identity.

    ### How to prove it

    Check OAuth and authentication Operators, pods, OAuth route, provider logs, CA trust, callback URL, cluster OAuth configuration, users, identities, and timestamps.

    ### Troubleshooting / operational approach

    Test with a controlled user, preserve break-glass access, and validate group or claim mapping before removing the prior provider.

    ### Production caution

    Never remove the last known administrative login path until the new provider is proven.

---

## Q5. Users, identities, and mappings?

    ### L3 answer

    An Identity represents a provider-specific login. A User is the OpenShift user record. UserIdentityMapping links them. Mapping methods determine whether identities claim, add to, or lookup users.

Duplicate or orphaned mappings can cause login conflicts or unexpected usernames.

    ### How to prove it

    Use `oc get users`, `oc get identities`, `oc get useridentitymappings`, and inspect OAuth mapping configuration and active bindings.

    ### Troubleshooting / operational approach

    Determine the authoritative provider and mapping method, then clean up with awareness of RBAC, groups, tokens, and future login behavior.

    ### Production caution

    Deleting users or identities blindly can break access or permit an unintended new mapping on the next login.

---

## Q6. How should service accounts be used?

    ### L3 answer

    A service account is a namespace-scoped workload identity. Pods receive projected tokens with audience and lifetime semantics. RBAC grants API permissions, and SCC access affects admission.

Use dedicated service accounts per responsibility and grant only required verbs and scope.

    ### How to prove it

    Inspect pod `serviceAccountName`, RoleBindings/ClusterRoleBindings, projected token configuration, SCC access, and audit usage.

    ### Troubleshooting / operational approach

    Replace default-service-account use, remove unused broad bindings, and use short-lived or federated credentials for external systems where possible.

    ### Production caution

    Copying service-account tokens into external scripts creates long-lived credential and rotation risk.

---

## Q7. How do you secure Secrets?

    ### L3 answer

    Secrets are API objects; base64 is encoding, not encryption. Protect them through RBAC, TLS, etcd encryption where required, external secret managers, rotation, audit, restricted service accounts, and backup protection.

Avoid secrets in Git, images, logs, shell history, and broad environment dumps.

    ### How to prove it

    Inventory secret readers, cluster-wide bindings, secret mount paths, etcd encryption status, audit records, and rotation age.

    ### Troubleshooting / operational approach

    Reduce access, move to approved secret-management flows, rotate exposed values, and validate applications with overlapping credentials.

    ### Production caution

    Enabling etcd encryption does not protect secrets after an authorized pod or user reads them.

---

## Q8. What is etcd encryption at rest?

    ### L3 answer

    The API server encrypts selected resource data before it is persisted in etcd. This reduces exposure from raw datastore access. OpenShift manages the process and key material through supported configuration.

Recovery depends on matching encryption keys and cluster state, so backup design must include encryption considerations.

    ### How to prove it

    Inspect the cluster encryption configuration and status, Operator conditions, and documented completion indicators. Verify backups follow the same-z-stream restore requirements.

    ### Troubleshooting / operational approach

    Enable or rotate only through supported procedures, monitor rewrite progress, and test restore in a controlled environment.

    ### Production caution

    Do not treat encryption at rest as a substitute for RBAC, application encryption, or backup security.

---

## Q9. How do you manage certificates?

    ### L3 answer

    First identify certificate ownership: OpenShift-generated, user-provided API/ingress, registry, proxy, or external service. Validate validity, SAN, key match, chain, trust, secret format, and consuming Operator.

Time drift and missing intermediates are common causes of TLS errors.

    ### How to prove it

    Use `openssl s_client`, inspect relevant secrets and custom resources, check Operator conditions, and compare endpoint certificates from client and node networks.

    ### Troubleshooting / operational approach

    Restore a known-good user certificate if needed, then correct the full chain and automate renewal. Let Operators manage generated certificates.

    ### Production caution

    Manual replacement of Operator-owned certificates can be reverted and may break internal trust.

---

## Q10. What is a good break-glass design?

    ### L3 answer

    Maintain an emergency identity independent of the normal identity provider, protected by strong authentication, vaulting, dual control, monitoring, and a documented network path. It is for emergencies only.

Every use should be time-bound, audited, linked to an incident/change, and followed by credential rotation.

    ### How to prove it

    Test access periodically under controlled conditions and verify the credentials, network route, API endpoint, and required runbook are available.

    ### Troubleshooting / operational approach

    Use break-glass only after normal access paths fail or policy authorizes emergency action. Record every command.

    ### Production caution

    A shared, routinely used cluster-admin password is not break-glass; it is an unmanaged privileged account.

---

## Q11. How do you audit who changed a resource?

    ### L3 answer

    Use Kubernetes/OpenShift audit records, managed fields, resource metadata, GitOps history, CI/CD logs, identity-provider records, and controller ownership. Events are not a durable audit trail.

The immediate actor may be an Operator; the originating human change may be on a parent custom resource or Git commit.

    ### How to prove it

    Correlate timestamp, user, source IP, user agent, verb, object, response, request ID, owner reference, and deployment history.

    ### Troubleshooting / operational approach

    Build a timeline from source change to reconciliation and impact. Preserve audit exports under approved handling.

    ### Production caution

    Do not attribute a controller-generated update directly to the controller without tracing who changed its desired state.

---

## Q12. How do you apply least privilege for support teams?

    ### L3 answer

    Define exact tasks and create role sets for read-only diagnosis, namespace operations, security administration, and platform administration. Bind enterprise groups, not individuals, and expire temporary privilege.

Review high-risk permissions: secret read, exec, impersonate, SCC use, rolebinding changes, node proxy, and cluster-scoped writes.

    ### How to prove it

    Test positive and negative actions with `oc auth can-i --as`. Export bindings and group membership for periodic review.

    ### Troubleshooting / operational approach

    Remove unused access, automate approvals and expiry, and document exceptions with owners.

    ### Production caution

    Starting from cluster-admin and subtracting a few verbs usually leaves excessive hidden privilege.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/authentication_and_authorization/understanding-authentication
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/etcd/backing-up-and-restoring-etcd-data

---

<!-- Original file: 08. Workload and Application Operations.md -->

# OpenShift Administrator L3 Interview Q&A — Workload and Application Operations

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Controllers, probes, images, configuration, quota, rollout, routes, finalizers, and onboarding.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. How do Deployment, StatefulSet, DaemonSet, Job, and CronJob differ?

    ### L3 answer

    A Deployment manages replaceable stateless replicas through ReplicaSets. A StatefulSet provides stable identity, ordered lifecycle, and commonly one PVC per replica. A DaemonSet runs a pod on each matching node. A Job runs work to completion, while a CronJob creates Jobs on a schedule.

The controller must match identity, persistence, placement, and completion requirements.

    ### How to prove it

    Inspect owner references, update strategy, replicas, pod identity, PVC templates, node selectors, and Job completion status.

    ### Troubleshooting / operational approach

    If a workload restarts incorrectly or cannot scale, verify that the chosen controller matches application semantics before tuning the platform.

    ### Production caution

    Do not place a stateful single-writer application behind a Deployment without a consistency and failover design.

---

## Q2. Readiness, liveness, and startup probes?

    ### L3 answer

    Readiness decides whether a pod receives Service traffic. Liveness decides whether a container should be restarted. Startup delays the other probes until slow initialization completes.

Bad probes can create self-inflicted outages: liveness may restart a slow but healthy application, readiness may remove every replica during dependency failure, and synchronized probe timing can amplify load.

    ### How to prove it

    Inspect probe configuration, pod events, restart count, previous logs, endpoint behavior, timeout, threshold, and application startup duration.

    ### Troubleshooting / operational approach

    Test the probe from inside the pod. Keep liveness independent of fragile external dependencies, make readiness represent ability to serve, and size startup for worst-case initialization.

    ### Production caution

    Do not solve probe failures by disabling probes permanently; correct the endpoint or thresholds and validate failure behavior.

---

## Q3. How do you troubleshoot CrashLoopBackOff?

    ### L3 answer

    CrashLoopBackOff is retry delay after repeated container failure, not the root cause. Common causes include bad command, configuration, secret, permissions, dependency, probe, resource limit, or application defect.

A container may also exit successfully because a one-time process was placed in a Deployment instead of a Job.

    ### How to prove it

    Use `oc describe pod`, current and `--previous` logs, termination reason/exit code, mounted config, command/args, security context, probes, and resource limits.

    ### Troubleshooting / operational approach

    Classify OOMKilled, application exit, permission, missing file, or probe restart. Correct the owning Deployment/StatefulSet or configuration and watch a controlled rollout.

    ### Production caution

    Repeated pod deletion resets evidence and backoff but does not fix the cause.

---

## Q4. How do you troubleshoot ImagePullBackOff?

    ### L3 answer

    ImagePullBackOff is retry delay after an image pull failure. Causes include wrong image, missing digest/tag, authentication, service-account pull secret, registry TLS/DNS, proxy, mirror mapping, rate limit, architecture, or node-local container storage.

Events normally include the registry response.

    ### How to prove it

    Inspect pod events, image reference, service account, linked pull secrets, registry reachability, node CRI-O logs, mirror configuration, and image availability.

    ### Troubleshooting / operational approach

    Test the exact digest from the affected node context, compare with a healthy node, and correct credentials, trust, proxy, or mirror content.

    ### Production caution

    Do not use floating tags for controlled production promotion when an immutable digest is required.

---

## Q5. How do ConfigMap and Secret updates reach pods?

    ### L3 answer

    Mounted projected files can update after propagation delay, but environment variables remain fixed until container restart. Applications may not reload changed files. A Deployment template does not change automatically just because a referenced ConfigMap or Secret changes.

Use versioned resources, checksum annotations, an approved reloader, or explicit rollout.

    ### How to prove it

    Inspect pod mounts and environment, resource versions, deployment template annotations, application reload logs, and actual file contents.

    ### Troubleshooting / operational approach

    Coordinate credential overlap, update the source, trigger a controlled rollout, and verify both new and rollback credentials where possible.

    ### Production caution

    Do not assume a Secret rotation is complete while old pods still run with old environment variables.

---

## Q6. How do ResourceQuota and LimitRange support multi-tenancy?

    ### L3 answer

    ResourceQuota limits aggregate namespace consumption such as CPU, memory, PVC capacity, load balancers, or object count. LimitRange sets defaults and min/max values for individual containers or objects.

They reduce noisy-neighbor risk and make scheduling predictable, but poor defaults can cause throttling, OOM, or admission rejection.

    ### How to prove it

    Use `oc get quota,limitrange -n <ns> -o yaml`, compare used/hard values, pod requests/limits, and rejection events.

    ### Troubleshooting / operational approach

    Align quotas with business ownership and capacity. Review exceptions, burst requirements, and historical use.

    ### Production caution

    Quota is governance, not physical reservation; failure-domain headroom still requires cluster capacity planning.

---

## Q7. What makes a safe rolling deployment?

    ### L3 answer

    Use multiple replicas, meaningful readiness, suitable `maxUnavailable` and `maxSurge`, enough capacity, backwards-compatible config and schema, and a tested rollback. Monitor error rate, latency, saturation, and business transactions.

For high-risk releases, use canary or blue-green. Separate irreversible database migration from application rollout.

    ### How to prove it

    Inspect Deployment strategy, rollout status, ReplicaSets, readiness, PDB, events, application metrics, and business checks.

    ### Troubleshooting / operational approach

    Pause or undo the rollout if health degrades and data compatibility allows it. Identify whether failure is platform, image, config, schema, or dependency.

    ### Production caution

    A completed rollout only means Kubernetes reached desired replicas; it does not prove the release is functionally correct.

---

## Q8. Application works in pod but not through Route. What do you check?

    ### L3 answer

    Trace the path: application listener, pod IP, Service targetPort, EndpointSlice, Service ClusterIP, router connectivity, Route admission, TLS mode, NetworkPolicy, load balancer, and DNS.

A common issue is listening only on localhost, wrong targetPort, failed readiness, or HTTP/TLS mismatch.

    ### How to prove it

    Use `ss -lntp` in the pod, direct pod/Service curls, `oc get svc,endpointslices,route -o yaml`, router logs, and external `curl -vk`.

    ### Troubleshooting / operational approach

    Fix the first failed hop and validate from both cluster and external client contexts.

    ### Production caution

    Do not modify DNS or the load balancer when the Service has no ready endpoints.

---

## Q9. Why can a resource remain Terminating?

    ### L3 answer

    Finalizers delay deletion until a controller completes cleanup. Namespace deletion can also wait for API discovery, remaining resources, unavailable API services, or external cleanup.

Finalizers protect against orphaning volumes, load balancers, DNS, and other external state.

    ### How to prove it

    Inspect `metadata.finalizers`, namespace conditions, API services, remaining resources, owner controllers, and external backend state.

    ### Troubleshooting / operational approach

    Restore the responsible controller or complete cleanup. Force-remove finalizers only after inventory, approval, and acceptance of orphaned state.

    ### Production caution

    Removing finalizers is not routine cleanup; it can leak infrastructure or lose data references.

---

## Q10. How should projects be onboarded in an enterprise cluster?

    ### L3 answer

    Use a standard GitOps or automation package that creates namespace ownership labels, quotas, LimitRanges, default-deny policies, required egress, service accounts, RBAC, secret integration, monitoring, backup classification, and cost metadata.

Define platform-owned and application-owned resources and require resource requests, probes, approved images, support contacts, and PDBs where appropriate.

    ### How to prove it

    Validate the generated namespace against policy, run a canary workload, test authorized and forbidden actions, and verify monitoring and backup inclusion.

    ### Troubleshooting / operational approach

    Use an exception workflow rather than allowing teams to manually bypass baseline controls.

    ### Production caution

    Manual namespace creation without ownership and policy creates long-term security and support debt.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/nodes/working-with-pods
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/networking_overview/networking_overview
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/authentication_and_authorization/understanding-authentication

---

<!-- Original file: 09. Monitoring Alerting Performance and Capacity.md -->

# OpenShift Administrator L3 Interview Q&A — Monitoring, Alerting, Performance, and Capacity

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Prometheus, alerting, API latency, node resources, cardinality, evidence, and capacity.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Describe the OpenShift monitoring architecture.

    ### L3 answer

    OpenShift platform monitoring is managed by the Cluster Monitoring Operator and uses Prometheus, Alertmanager, and related components. User workload monitoring can collect application metrics through a supported separate path.

ServiceMonitor and PodMonitor discover targets, PrometheusRule defines alerts and recording rules, and Alertmanager routes notifications. Configuration should use supported resources, not edits to generated Deployments.

    ### How to prove it

    Inspect Cluster Monitoring Operator, pods and PVCs in monitoring namespaces, targets, rules, Alertmanager status, and supported configuration ConfigMaps.

    ### Troubleshooting / operational approach

    Classify collection, storage, query, alert evaluation, or notification failure. Verify a sample target and alert end to end.

    ### Production caution

    High-cardinality user metrics can destabilize shared monitoring; governance is required.

---

## Q2. Alert versus incident?

    ### L3 answer

    An alert is a rule signal. An incident is service impact requiring coordinated response. Good alerts are actionable, owned, deduplicated, and connected to runbooks.

Triage must confirm whether the alert is firing, pending, stale, silenced, or inhibited and whether users are affected.

    ### How to prove it

    Review alert labels, annotations, start time, related alerts, dashboards, logs, and a business transaction.

    ### Troubleshooting / operational approach

    Identify the first signal and shared dependency. Mitigate impact, then tune the rule only after root cause.

    ### Production caution

    Silencing noisy alerts without remediation creates blind spots and audit risk.

---

## Q3. Why are Prometheus targets down?

    ### L3 answer

    Common causes are incorrect ServiceMonitor/PodMonitor selectors, namespace selection, Service labels, named port mismatch, wrong scheme/path, TLS, RBAC, NetworkPolicy, or an unavailable metrics endpoint.

A Running pod does not prove the metrics endpoint is reachable.

    ### How to prove it

    Inspect target error, monitor YAML, Service and EndpointSlice, port names, endpoint curl, certificate trust, and network policy.

    ### Troubleshooting / operational approach

    Fix discovery or endpoint configuration. Check scrape duration, payload size, and cardinality before increasing timeouts.

    ### Production caution

    Do not broaden NetworkPolicy or TLS trust cluster-wide for one misconfigured target.

---

## Q4. How do you investigate API-server latency?

    ### L3 answer

    Determine whether latency affects reads, writes, one API group, or all traffic. Check API and etcd health, request duration and inflight metrics, webhooks, aggregated APIs, etcd fsync/commit, control-plane CPU/memory, and API Priority and Fairness.

Large list operations, a misbehaving controller, or slow admission webhook can create widespread delay.

    ### How to prove it

    Use API readiness, Operator conditions, Prometheus metrics, audit/request logs, webhook status, etcd metrics, and control-plane node performance.

    ### Troubleshooting / operational approach

    Correlate start time with automation, deployment, backup, update, or catalog activity. Isolate the expensive request or dependency.

    ### Production caution

    Do not restart all API servers or etcd members. Maintain quorum and compare instances.

---

## Q5. How do you investigate high node CPU?

    ### L3 answer

    Separate user, system, iowait, steal, interrupts, and throttling. Identify top containers and host processes, pod requests/limits, run queue, kubelet/CRI-O activity, monitoring cardinality, and logging volume.

High CPU can delay kubelet and networking and create secondary platform alerts.

    ### How to prove it

    Use `oc adm top nodes/pods`, pod metrics, node debug with `top`, `pidstat`, `mpstat`, cgroup data, and relevant alerts.

    ### Troubleshooting / operational approach

    Mitigate by scaling, moving workload, correcting limits/requests, fixing a hot loop, reducing cardinality, or adding capacity. Validate latency afterward.

    ### Production caution

    Raising CPU limits does not create CPU; it may increase contention for critical platform processes.

---

## Q6. How do you investigate memory pressure and OOM?

    ### L3 answer

    Distinguish container cgroup OOM, node-level OOM, kubelet eviction, and application leak. A container can be OOMKilled at its limit while the node has free memory. Node MemoryPressure can evict pods.

Inspect working set, cache, slab, requests/limits, kernel logs, and rollout timing.

    ### How to prove it

    Use pod termination state, events, `oc adm top`, node conditions, node journal/kernel logs, and application heap diagnostics.

    ### Troubleshooting / operational approach

    Capture diagnostics before restart when safe, correct leak or sizing, and preserve platform headroom.

    ### Production caution

    Removing memory limits can convert a contained failure into node instability.

---

## Q7. What is metric cardinality?

    ### L3 answer

    Cardinality is the number of unique time series. Labels with unbounded values such as user ID, request ID, full URL, or pod UID can create explosive growth and consume Prometheus memory, CPU, storage, and query time.

The long-term fix is instrumentation and label governance, not only larger Prometheus pods.

    ### How to prove it

    Identify metrics with highest series count and label dimensions, ingestion rate, TSDB growth, scrape size, and query cost.

    ### Troubleshooting / operational approach

    Remove or relabel unneeded dimensions, use recording rules, set standards, and enforce team budgets.

    ### Production caution

    Dropping metrics without owner review can remove required alerting or compliance evidence.

---

## Q8. How do you create a capacity report?

    ### L3 answer

    Combine allocatable capacity, requests, limits, actual utilization, historical peaks, growth, fragmentation, failure-domain headroom, storage performance, pod/object counts, and planned demand.

Average utilization is insufficient. Model N+1 or zone loss, update surge, and seasonal peaks.

    ### How to prove it

    Use historical monitoring, node/machine inventory, quotas, pending pods, storage metrics, and business forecasts. State assumptions.

    ### Troubleshooting / operational approach

    Recommend capacity with a trigger date, amount, risk if delayed, and validation plan.

    ### Production caution

    Do not count control-plane or reserved capacity as freely available tenant capacity.

---

## Q9. What evidence should be captured before restart?

    ### L3 answer

    Capture resource YAML, describe, current and previous logs, events, placement, node conditions, Operator conditions, metrics, recent changes, timestamps, and dependency status. For node issues, preserve journal and system resource evidence.

Restart can clear transient state, change leaders, and destroy the original failure pattern.

    ### How to prove it

    Use a standard evidence bundle and timestamp it. Run must-gather for significant platform incidents before disruptive remediation when possible.

    ### Troubleshooting / operational approach

    Restart only as a documented mitigation with expected effect, risk, rollback, and post-restart validation.

    ### Production caution

    “Restart fixed it” is not root cause and may hide a recurring capacity, certificate, or race condition.

---

## Q10. How do you monitor user workloads safely?

    ### L3 answer

    Enable supported user workload monitoring and let teams define ServiceMonitor, PodMonitor, and PrometheusRule within governed namespaces. Set standards for scrape interval, timeout, labels, alert ownership, and retention.

Keep platform monitoring under platform-team control and use remote write or external systems when justified.

    ### How to prove it

    Review user monitoring configuration, target count, series growth, rules, namespace RBAC, and Alertmanager routing.

    ### Troubleshooting / operational approach

    Onboard teams with templates and cardinality review. Detect and contain abusive metrics before they affect the platform.

    ### Production caution

    Do not let tenant teams edit core monitoring resources or route unreviewed alerts to shared paging channels.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/monitoring/about-openshift-container-platform-monitoring
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/support/gathering-cluster-data

---

<!-- Original file: 10. Cluster Update and Lifecycle Management.md -->

# OpenShift Administrator L3 Interview Q&A — Cluster Update and Lifecycle Management

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Prechecks, conditional updates, CVO/MCO progress, disconnected updates, and validation.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. What are mandatory prechecks before an OpenShift update?

    ### L3 answer

    Verify supported update path/channel, ClusterVersion, all required Cluster Operators, MachineConfigPools, nodes, storage, ingress, DNS, authentication, critical alerts, CSRs, capacity, and business readiness. Review release notes, removed APIs, third-party Operator compatibility, webhooks, PDBs, paused pools, and custom MachineConfigs.

Take an etcd backup from the current z-stream and verify application backups.

    ### How to prove it

    Capture `oc adm upgrade`, ClusterVersion, CO, MCP, nodes, APIRequestCounts, PDBs, OLM resources, alerts, and backup checksum in the change record.

    ### Troubleshooting / operational approach

    Resolve unexplained degradation before starting. Define stop criteria, support contacts, communication, and post-update canaries.

    ### Production caution

    Do not start an update from an unhealthy cluster merely because a maintenance window is open.

---

## Q2. What is a conditional update?

    ### L3 answer

    A conditional update has a known risk that may apply only to clusters matching certain conditions. The update service presents the risk description and evaluation guidance.

The administrator must determine exposure and complete any mitigation or acknowledgment.

    ### How to prove it

    Read the exact conditional risk, compare affected platform/configuration, release notes, and Red Hat guidance. Record the decision.

    ### Troubleshooting / operational approach

    If exposed, remediate or choose another supported target. If proceeding under exception, increase monitoring and confirm recovery options.

    ### Production caution

    Do not force a conditional update based only on schedule pressure.

---

## Q3. How do you monitor an update?

    ### L3 answer

    Track ClusterVersion history and conditions, Cluster Operators, MachineConfigPools, node versions/readiness, events, and critical applications. Identify the component currently progressing and its reason.

Temporary replica movement can be normal, but quorum and designed availability must remain.

    ### How to prove it

    Use `oc get clusterversion`, `oc get co`, `oc get mcp`, node versions, events, and canary transactions throughout the update.

    ### Troubleshooting / operational approach

    If progress stalls, isolate the Operator or node and troubleshoot that layer. Preserve logs before intervention.

    ### Production caution

    Do not reboot multiple nodes or alter release configuration while an update is active without documented guidance.

---

## Q4. What does Upgradeable=False mean?

    ### L3 answer

    A Cluster Operator reports Upgradeable=False when it detects a condition that makes an update unsafe or unsupported. The reason/message identifies the required remediation or acknowledgment.

It is a safety control, not a cosmetic warning.

    ### How to prove it

    Inspect the Upgradeable condition on all Cluster Operators and the ClusterVersion available-updates information.

    ### Troubleshooting / operational approach

    Resolve the stated condition, verify it clears, and record evidence. Use overrides only with official guidance and approved risk.

    ### Production caution

    Forcing past the condition can leave a failed or unsupported cluster.

---

## Q5. Update stuck on MachineConfigPool?

    ### L3 answer

    Find nodes where current and desired rendered configs differ. Check pool conditions, MachineConfigDaemon logs, drain blockers, disk, kubelet, CRI-O, reboot state, and node reachability.

The pool may wait on a PDB, unavailable workload, invalid MachineConfig, or host failure.

    ### How to prove it

    Use `oc describe mcp`, node config annotations, MCD logs, node events, PDBs, and host journals.

    ### Troubleshooting / operational approach

    Correct the source configuration or node problem and allow MCO to reconcile. Maintain enough spare capacity for rollout.

    ### Production caution

    Never manually mark a node updated or edit rendered configuration status.

---

## Q6. Can you roll back a minor OpenShift update?

    ### L3 answer

    OpenShift updates are designed as forward lifecycle operations; a simple in-place minor-version rollback is not the normal supported recovery. Recovery may require completing the update, applying a fix, replacing a node, or a Red Hat-supported disaster procedure.

An etcd backup is not a general downgrade tool and documented restore requires the same z-stream.

    ### How to prove it

    Review current and target versions, update history, failure component, backup metadata, and support guidance.

    ### Troubleshooting / operational approach

    Choose the supported recovery path based on failure. Preserve application data and external dependencies separately.

    ### Production caution

    Never advertise “we will restore etcd” as the standard rollback plan for a minor update.

---

## Q7. Why check third-party Operators first?

    ### L3 answer

    Operators may use APIs removed by the target Kubernetes version, install webhooks, own CRDs, or manage critical storage, network, security, and application services. Compatibility and upgrade order matter.

Some Operators must be upgraded before OpenShift and others afterward.

    ### How to prove it

    Inventory Subscriptions, channels, CSVs, CRDs, webhooks, vendor matrices, and test-cluster results.

    ### Troubleshooting / operational approach

    Upgrade Operators in the supported sequence and verify operands, not only CSV status.

    ### Production caution

    A healthy platform update can still leave an incompatible add-on service unavailable.

---

## Q8. How do you validate after an update?

    ### L3 answer

    Wait for ClusterVersion and required Operators to converge, MCPs and nodes to update, and platform services to become healthy. Validate API, DNS, ingress, registry pull, OAuth, storage provision/attach, monitoring, logging, and representative applications.

Compare alerts, restarts, performance, and certificates with baseline.

    ### How to prove it

    Run an automated post-update test suite across zones and namespaces and capture output in the change record.

    ### Troubleshooting / operational approach

    Keep the change open until business transactions and scheduled jobs pass.

    ### Production caution

    Do not close the update only because `oc get co` shows green.

---

## Q9. How do you update a disconnected cluster?

    ### L3 answer

    Mirror the target release and required metadata using a connected environment, transfer and verify content, and ensure mirror configuration resolves every digest. Mirror compatible Operator catalogs and operand images separately.

The cluster still needs the same prechecks, backup, update path, and validation.

    ### How to prove it

    Verify image digests, signatures/metadata as required, registry trust, catalog graph, available update path, and prior content retention.

    ### Troubleshooting / operational approach

    Test in a disconnected lower environment, then promote immutable content. Keep previous content through the recovery window.

    ### Production caution

    A missing bundle or operand image may not appear until an Operator reconciles after the platform update.

---

## Q10. What change controls belong around updates?

    ### L3 answer

    Require compatibility review, risk assessment, tested procedure, backups, approval, maintenance communication, owner coverage, stop conditions, command logging, support plan, and post-change validation.

Separate preparation, approval, execution, and observation where required.

    ### How to prove it

    Use a change record with exact versions, evidence, timeline, deviations, and sign-off.

    ### Troubleshooting / operational approach

    Perform a post-update review of duration, issues, alert quality, and runbook improvements.

    ### Production caution

    Unrecorded emergency commands during an update make recovery and support much harder.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/updating_clusters/performing-a-cluster-update
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/release_notes/ocp-4-21-release-notes
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/etcd/backing-up-and-restoring-etcd-data

---

<!-- Original file: 11. etcd Backup Restore and Disaster Recovery.md -->

# OpenShift Administrator L3 Interview Q&A — etcd, Backup, Restore, and Disaster Recovery

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** etcd snapshots, application backup, quorum recovery, RPO/RTO, and DR exercises.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. What does an etcd backup protect?

    ### L3 answer

    An etcd backup protects API state stored in etcd and the required static-pod resource files collected by the supported script. It includes cluster resources, configuration, secrets, workload objects, and status at the snapshot time.

It does not protect application data in PVs, external databases, object storage, DNS, load balancers, cloud resources, or registry content unless separately backed up.

    ### How to prove it

    Inventory backup contents, cluster version, timestamp, checksum, encryption status, and separate application backup coverage.

    ### Troubleshooting / operational approach

    Map every critical service to cluster-state backup, data backup, image/config preservation, and external dependency recovery.

    ### Production caution

    An etcd snapshot alone is not a complete OpenShift DR solution.

---

## Q2. How should etcd backups be taken and stored?

    ### L3 answer

    Run the documented cluster backup script once on one control-plane host. Current Red Hat guidance requires a backup from the same z-stream for restore. Take backups regularly and before updates or major configuration changes.

Copy them off-node to encrypted, access-controlled, protected storage in another failure domain.

    ### How to prove it

    Record cluster ID, exact release, timestamp, files, checksum, backup location, encryption, retention, and restore-test result.

    ### Troubleshooting / operational approach

    Automate backup and verification, monitor failures, and periodically perform a controlled restore exercise.

    ### Production caution

    Do not run independent backups on every etcd member or leave the only copy on a control-plane filesystem.

---

## Q3. When is etcd restore appropriate?

    ### L3 answer

    Restore is a last-resort action for catastrophic state loss such as majority control-plane/etcd loss or critical deletion that cannot be recovered normally. It reverts the entire API state to the backup time and can conflict with external systems and data changed afterward.

If the API is available, prefer targeted recovery, GitOps, backup restore, or member replacement.

    ### How to prove it

    Verify quorum status, API availability, exact z-stream backup, checksum, encryption keys, application data state, and support engagement.

    ### Troubleshooting / operational approach

    Freeze changes, communicate expected data loss, follow the exact documented procedure, and validate every platform service afterward.

    ### Production caution

    Restoring a live, usable cluster can be destructive and destabilizing.

---

## Q4. Single unhealthy etcd member versus full restore?

    ### L3 answer

    With one failed member and a healthy majority, replace or recover that member using the documented member procedure. This preserves current state. Full restore rebuilds cluster state from a past snapshot and is reserved for quorum loss or unrecoverable critical state.

The choice is based on quorum and state availability.

    ### How to prove it

    Inspect etcd pods/members, node health, Operator conditions, API availability, and control-plane infrastructure.

    ### Troubleshooting / operational approach

    Preserve the majority, recover one member at a time, and verify leader/quorum stability.

    ### Production caution

    Do not perform full restore for a single-member problem.

---

## Q5. How does OADP/application backup differ from etcd backup?

    ### L3 answer

    Application backup tools protect selected Kubernetes resources and persistent-volume data through CSI snapshots or file-system mechanisms. They support namespace/application restore and migration. etcd backup captures global cluster API state for control-plane disaster recovery.

Most enterprises need both.

    ### How to prove it

    Review backup CR status, included/excluded resources, PV method, warnings, object storage, hooks, and restore test. Separately verify etcd backup.

    ### Troubleshooting / operational approach

    Test application-consistent recovery into a controlled namespace or cluster and validate data, secrets, routes, and storage mapping.

    ### Production caution

    A Completed backup with warnings or untested snapshots may not meet the application RPO.

---

## Q6. Define RPO and RTO.

    ### L3 answer

    Recovery Point Objective is the maximum acceptable data loss measured in time. Recovery Time Objective is the maximum acceptable time to restore service. Define them per business service, including databases and external dependencies, not only per cluster.

RPO drives backup/replication frequency; RTO includes detection, decision, access, provisioning, restore, DNS, and validation.

    ### How to prove it

    Measure actual backup intervals, restore durations, dependency startup, and business validation from exercises.

    ### Troubleshooting / operational approach

    Close gaps with more frequent backup, automation, pre-provisioned capacity, replication, or simpler recovery procedures.

    ### Production caution

    An infrastructure RTO is meaningless if application data restoration exceeds it.

---

## Q7. What should a DR exercise include?

    ### L3 answer

    Test backup retrieval and checksum, credentials, infrastructure, etcd or application restore, storage, images, DNS/load-balancer cutover, OAuth, certificates, policy, monitoring, and a business transaction.

Include one-node, zone, storage, namespace deletion, and control-plane quorum scenarios.

    ### How to prove it

    Capture actual RPO/RTO, manual steps, failed dependencies, decisions, and evidence.

    ### Troubleshooting / operational approach

    Update runbooks, automation, ownership, and capacity based on measured results.

    ### Production caution

    A tabletop discussion does not replace technical restore testing.

---

## Q8. How do you shut down and restart gracefully?

    ### L3 answer

    Follow the version-specific documented sequence, quiesce or stop stateful applications as required, and take an etcd backup before shutdown. Preserve control-plane quorum during the process and account for load balancers, storage, and infrastructure ordering.

On restart, restore dependencies and control plane in the supported order, then workers and applications.

    ### How to prove it

    Record backup, node and application state, storage detach, shutdown order, and on restart monitor etcd, API, Operators, MCPs, nodes, ingress, and storage.

    ### Troubleshooting / operational approach

    Use a command checklist and stop if quorum or storage behavior differs from expected.

    ### Production caution

    Powering off every node without preparation can cause etcd, storage, and application consistency problems.

---

## Q9. How do you protect backups from ransomware or deletion?

    ### L3 answer

    Use a separate security boundary, least-privilege credentials, encryption, immutability/object lock, versioning, offline or isolated copies, multi-party delete control, audit, and credential recovery.

Separate backup write from retention/deletion administration where possible.

    ### How to prove it

    Review object-lock status, IAM, deletion logs, versioning, restore credentials, retention, and alerts for failed or unusual operations.

    ### Troubleshooting / operational approach

    Test restore using credentials and paths available during a security incident, not only normal operations.

    ### Production caution

    Cluster-admin should not automatically have authority to erase all backup generations.

---

## Q10. What belongs in a DR runbook?

    ### L3 answer

    Include scope, decision authority, prerequisites, exact release applicability, backup locations/checksums, credentials, infrastructure dependencies, supported commands, expected output, stop conditions, escalation, communication, validation, and failback.

Store a copy outside the cluster and update after every exercise.

    ### How to prove it

    Review runbook version, owner, last test, measured timings, and accessibility during identity/network outage.

    ### Troubleshooting / operational approach

    Run a periodic exercise with someone other than the author to expose hidden assumptions.

    ### Production caution

    A runbook that depends on inaccessible internal documentation or one person's memory is not resilient.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/etcd/overview-of-etcd
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/etcd/backing-up-and-restoring-etcd-data
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/backup_and_restore/backup-restore-overview

---

<!-- Original file: 12. L3 Troubleshooting and Support Methodology.md -->

# OpenShift Administrator L3 Interview Q&A — L3 Troubleshooting and Support Methodology

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Incident triage, evidence, degraded Operators, nodes, pods, authentication, registry, and support.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. Describe your L3 troubleshooting methodology.

    ### L3 answer

    Establish impact, scope, start time, and recent change. Preserve evidence and create a timeline. Follow the failed transaction through dependencies, form a testable hypothesis, make the smallest safe change, and validate recovery and side effects.

Separate symptom from cause and platform from application ownership.

    ### How to prove it

    Collect ClusterVersion/Operator/node health, events, logs, metrics, audit/change history, and a representative transaction. Timestamp evidence.

    ### Troubleshooting / operational approach

    Use layers: client, DNS/load balancer, ingress, Service, pod, node, network, storage, control plane, and external dependency. Communicate facts, uncertainty, and next decision.

    ### Production caution

    Random restarts, simultaneous changes, and undocumented commands make root cause and rollback harder.

---

## Q2. What does `oc adm must-gather` do?

    ### L3 answer

    Must-gather collects cluster resources, logs, events, and diagnostics through a standard image. Product-specific must-gather images can collect additional storage, networking, or Operator evidence.

Run it while the issue is present and before disruptive action when possible.

    ### How to prove it

    Use `oc adm must-gather --dest-dir=<secure-path>` and `oc adm inspect` for targeted resources. Record time, scope, and command.

    ### Troubleshooting / operational approach

    Protect the archive, review size, and attach it to support with a clear timeline and impact. Supplement with external LB, storage, application, or packet evidence.

    ### Production caution

    Must-gather may contain sensitive metadata and configuration; handle it under company policy.

---

## Q3. How do you troubleshoot multiple degraded Cluster Operators?

    ### L3 answer

    Look for a shared dependency such as API, etcd, DNS, ingress, network, storage, node, certificate, proxy, or registry. Sort conditions by timestamp and identify the first degradation and common error.

Many secondary Operators can degrade because one core service failed.

    ### How to prove it

    Collect `oc get co`, describe affected Operators, events, node/MCP state, API readiness, and critical namespace pods.

    ### Troubleshooting / operational approach

    Build a dependency map and fix the earliest common failure. Watch reconciliation restore downstream Operators.

    ### Production caution

    Restarting every degraded Operator hides the dependency chain and may create additional failures.

---

## Q4. Why are `oc` commands slow?

    ### L3 answer

    Possible layers include client DNS/TLS/network, OAuth, API server, admission webhooks, aggregated APIs, etcd latency, control-plane resource pressure, or expensive list/watch requests.

Compare one command, one resource, and one client location rather than calling it a generic network issue.

    ### How to prove it

    Measure DNS, TCP/TLS, API readyz, request duration, webhook/APIService health, etcd metrics, control-plane CPU/disk, and audit request patterns.

    ### Troubleshooting / operational approach

    Identify whether reads, writes, or one API group are slow and correlate with automation, backup, scans, or deployment changes.

    ### Production caution

    Do not run repeated cluster-wide list commands during API overload; they can worsen the condition.

---

## Q5. How do you troubleshoot DiskPressure?

    ### L3 answer

    DiskPressure or inode pressure can result from container images, writable layers, logs, emptyDir, journal, or another mount. It can cause eviction, failed image pulls, and container creation failure.

Identify the actual filesystem and owner before cleanup.

    ### How to prove it

    Use node describe, `df -h`, `df -i`, controlled `du`, journal usage, CRI-O image inventory, pod ephemeral-storage requests/limits, and log rotation.

    ### Troubleshooting / operational approach

    Stop the growth source, use supported image/log cleanup, move or resize storage, and validate kubelet pressure clears.

    ### Production caution

    Never manually delete CRI-O overlay files; that can corrupt container storage.

---

## Q6. Pod stuck ContainerCreating?

    ### L3 answer

    ContainerCreating often indicates image pull, pod sandbox/CNI, CSI attach/mount, missing Secret/ConfigMap, runtime, or node pressure. Events identify the subsystem.

`FailedCreatePodSandBox` points toward CNI/runtime; `FailedMount` toward storage/config; image errors toward registry.

    ### How to prove it

    Use pod describe/events, CNI and CSI node pod logs, CRI-O/kubelet logs, PVC and VolumeAttachment, secrets/configmaps, and node conditions.

    ### Troubleshooting / operational approach

    Fix the first event-producing dependency and recreate only if the controller does not recover automatically.

    ### Production caution

    Deleting the pod repeatedly can move it to other nodes and hide a node-specific failure.

---

## Q7. How do you handle Pending CSRs?

    ### L3 answer

    Identify signer, requesting identity, node name, addresses, and whether the host is expected. Kubelet client and serving CSRs have different purposes and approval behavior.

Pending CSRs can block node join or node metrics/exec functions.

    ### How to prove it

    Use `oc get csr`, `oc describe csr`, decode/request details as appropriate, compare Machine/Node inventory, and check bootstrap activity.

    ### Troubleshooting / operational approach

    Approve only verified requests. Investigate floods, hostname churn, or unexpected sources.

    ### Production caution

    Blanket CSR approval is a security risk and can admit unauthorized nodes.

---

## Q8. Namespace stuck Terminating?

    ### L3 answer

    Check namespace conditions, unavailable API services, remaining namespaced resources, and finalizers. Namespace deletion needs API discovery and controller cleanup.

Third-party APIService or webhook failure can block deletion.

    ### How to prove it

    Use `oc describe namespace`, inspect finalizers, `oc get apiservices`, and enumerate remaining resources through supported queries.

    ### Troubleshooting / operational approach

    Restore the API/controller, complete cleanup, and force-finalize only after external side-effect review and approval.

    ### Production caution

    Force finalization can orphan volumes, load balancers, and custom resources.

---

## Q9. Intermittent OAuth login failures?

    ### L3 answer

    Investigate OAuth Operator, authentication pods, OAuth route/ingress, identity-provider reachability, DNS, TLS, time, rate limits, callback configuration, and mapping.

Compare successful and failed login timestamps to separate provider and platform behavior.

    ### How to prove it

    Collect OAuth/authentication logs, provider logs, endpoint TLS, route status, DNS, and user/identity mappings.

    ### Troubleshooting / operational approach

    Preserve break-glass access, correct the failing dependency, and test token issuance and API use.

    ### Production caution

    Do not remove the identity provider during an active incident without a proven alternative administrator path.

---

## Q10. Registry push failure?

    ### L3 answer

    Separate authentication/authorization, route/TLS, storage, quota, registry pod, image layer upload, proxy, and timeout. A push can fail after some layers upload because of backend storage or ingress timeout.

Internal Service and external route paths may behave differently.

    ### How to prove it

    Check image-registry Operator, registry deployment, route, storage/PVC or object backend, logs, user access to ImageStream, quota, and external curl.

    ### Troubleshooting / operational approach

    Test a small controlled push internally and externally. Correct storage, TLS, permission, or timeout at the owning layer.

    ### Production caution

    Do not switch production registry to ephemeral storage to bypass a persistent-storage incident.

---

## Q11. What if there is no known recent change?

    ### L3 answer

    “No change” may hide certificate expiry, data growth, capacity threshold, scheduled job, external dependency change, token rotation, catalog refresh, infrastructure maintenance, hardware fault, or traffic pattern shift.

Use evidence to discover time-based and external changes.

    ### How to prove it

    Correlate metrics, alerts, audit, cron jobs, deployments, certificate inventory, provider logs, storage growth, and network maintenance.

    ### Troubleshooting / operational approach

    Compare affected versus healthy node/zone/namespace and form controlled hypotheses.

    ### Production caution

    Do not let the absence of a change ticket end root-cause analysis.

---

## Q12. When should you open a Red Hat support case?

    ### L3 answer

    Engage when impact is high, a platform defect or data-integrity risk is suspected, documented recovery is insufficient, or destructive actions such as etcd restore are considered. Open early for quorum loss, failed updates, or widespread storage/network failure.

A strong case includes impact, severity, cluster ID/version, timeline, changes, errors, actions, and diagnostics.

    ### How to prove it

    Attach must-gather and product-specific diagnostics, command output, timestamps, and contact availability under approved data handling.

    ### Troubleshooting / operational approach

    Continue updating the case with every change and decision. Ask for action-oriented guidance and confirm supportability.

    ### Production caution

    Do not wait until after evidence-destroying restarts or unsupported changes to engage support.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/support/gathering-cluster-data
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/architecture/control-plane
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/machine_configuration/machine-config-index

---

<!-- Original file: 13. Corporate Production Scenario Questions.md -->

# OpenShift Administrator L3 Interview Q&A — Corporate Production Scenarios

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** Real production incidents involving nodes, networking, storage, security, updates, and DR.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. After reboot, a worker is Ready but SchedulingDisabled.

### L3 answer

The node is healthy enough to report Ready, but it remains cordoned. Confirm whether this is an incomplete maintenance action, an automation policy, or a deliberate hold. Ready and schedulable are independent states.

### How to prove it

Use `oc get node`, `oc describe node`, review maintenance/change records, MCO convergence, CNI/CSI daemon pods, kubelet/CRI-O, labels, taints, and available capacity.

### Troubleshooting / operational approach

After all checks pass, run `oc adm uncordon <node>` and monitor new pod placement and application health.

### Production caution

Do not uncordon only because Ready=True; verify the maintenance objective and platform agents first.

---

## Q2. A pod is Pending although the cluster shows free resources.

### L3 answer

Total free CPU and memory do not prove that one eligible node can satisfy the pod. Taints, affinity, topology spread, huge pages, GPU/device requests, PVC topology, quota, and pod-density limits can block scheduling.

### How to prove it

Read the scheduler message in `oc describe pod`, inspect node labels/taints, requests, PVC/StorageClass, quota, and topology constraints.

### Troubleshooting / operational approach

Fix the exact constraint or add matching capacity. Validate that placement still meets resiliency and storage requirements.

### Production caution

Do not reduce requests below real application need simply to make the scheduler accept the pod.

---

## Q3. All Routes return 503 after a network change.

### L3 answer

A simultaneous failure across applications points to shared ingress or network dependencies, not every application. Check ingress Operator, router pods, router Service/endpoints, OVN, load balancer, MTU, and wildcard DNS.

### How to prove it

Use `oc get co ingress network dns`, router logs, IngressController status, route admission, backend connectivity, and external load-balancer health.

### Troubleshooting / operational approach

Identify the first shared broken hop and roll back or correct the network change. Validate an internal Service and an external route.

### Production caution

Do not restart application pods across all namespaces; that increases impact without addressing the shared path.

---

## Q4. Only one namespace cannot reach external APIs.

### L3 answer

Namespace-scoped policy or egress configuration is likely. Check NetworkPolicy, EgressFirewall, AdminNetworkPolicy interaction, egress IP, DNS, external source-IP allowlist, and application proxy settings.

### How to prove it

Compare a test pod in the affected namespace with a known-good namespace. Inspect policies, namespace labels, egress resources, DNS result, source IP, and external firewall logs.

### Troubleshooting / operational approach

Correct the narrow policy or assignment and retest both allowed and denied destinations.

### Production caution

Do not broaden cluster-wide egress to solve one namespace configuration problem.

---

## Q5. A StatefulSet pod reports Multi-Attach after node failure.

### L3 answer

The RWO volume is probably still attached or considered attached to the failed node. Data integrity depends on fencing the old writer before detach and attach elsewhere.

### How to prove it

Inspect pod events, VolumeAttachment, old node and pod status, CSI attacher logs, storage backend, and infrastructure power state.

### Troubleshooting / operational approach

Prove the old node cannot write, perform supported detach/recovery, start the pod, and validate filesystem/application consistency.

### Production caution

Never force-detach while the old node may still be active.

---

## Q6. Worker MachineConfigPool has been Updating for hours.

### L3 answer

One or more nodes have not reached the desired rendered config. Causes include blocked drain, bad MachineConfig, node outage, disk full, kubelet/CRI-O failure, or reboot failure.

### How to prove it

Use `oc describe mcp worker`, node current/desired config annotations, MCD logs, PDBs, node events, and host journals.

### Troubleshooting / operational approach

Fix the source config or affected node, then let MCO reconcile. Maintain capacity and observe the next node before continuing.

### Production caution

Do not edit rendered MachineConfig or node annotations to bypass state.

---

## Q7. Users authenticate but receive Forbidden in one project.

### L3 answer

Authentication is successful; authorization is not. Check group membership, RoleBinding subjects, namespace, ClusterRole rules, resource subresources, and requested verb.

### How to prove it

Use `oc auth can-i ... --as=<user>`, `oc auth can-i --list`, RoleBindings, groups, and `oc adm policy who-can`.

### Troubleshooting / operational approach

Grant the minimum missing permission to the appropriate group and retest positive and negative actions.

### Production caution

Do not grant cluster-admin or a cluster-wide binding for a namespace-specific support task.

---

## Q8. A pod is OOMKilled after a release while node memory is free.

### L3 answer

The container exceeded its cgroup limit or an application-level memory configuration, independent of overall node free memory. The release may have increased heap, cache, concurrency, or leak behavior.

### How to prove it

Inspect termination reason, memory limit/request, application logs, heap settings, metrics, and previous version baseline.

### Troubleshooting / operational approach

Capture memory evidence, fix the leak or sizing, test, and roll out. Reassess node headroom before raising limits.

### Production caution

Removing the limit can turn a contained application failure into node-wide MemoryPressure.

---

## Q9. API latency spikes every night at 02:00.

### L3 answer

The regular timing suggests scheduled jobs, backups, scans, GitOps sync, monitoring queries, or external automation. Correlate API request rate, etcd latency, webhook duration, and object churn.

### How to prove it

List CronJobs and scheduled platform tasks, inspect audit/request metrics, etcd fsync/commit, control-plane resources, and backup timelines.

### Troubleshooting / operational approach

Prove causality, reschedule or rate-limit the workload, and validate latency on the next run.

### Production caution

Do not increase API resources without identifying unbounded request behavior.

---

## Q10. CSV is Succeeded but the managed database is unavailable.

### L3 answer

CSV Succeeded proves OLM installed the Operator, not that the operand is healthy. The database can fail from bad CR configuration, storage, certificate, capacity, service, or application issues.

### How to prove it

Inspect Operator deployment, custom resource conditions/observed generation, database pods, storage, services, alerts, and a database transaction.

### Troubleshooting / operational approach

Separate management-plane and data-plane failures, correct the operand dependency, and confirm reconciliation.

### Production caution

Do not reinstall the Operator before preserving database and custom-resource evidence.

---

## Q11. Cluster update is blocked by Upgradeable=False.

### L3 answer

A core Operator has identified an unsafe or unsupported condition. The exact reason may require configuration remediation, API cleanup, or an acknowledgment.

### How to prove it

Inspect Upgradeable conditions on Cluster Operators, ClusterVersion messages, release notes, and related configuration.

### Troubleshooting / operational approach

Resolve the condition and verify it clears. Use any override only with official guidance and approved risk.

### Production caution

Forcing the update can create a failed or unsupported cluster.

---

## Q12. One control-plane node is down but API remains available.

### L3 answer

Two healthy members can preserve quorum in a typical three-member control plane. Protect that majority and identify whether the failed node has infrastructure, host, static-pod, or etcd-member failure.

### How to prove it

Check nodes, etcd and API pods, Cluster Operators, infrastructure status, and member health.

### Troubleshooting / operational approach

Recover or replace the one failed member using supported steps and confirm quorum, leader stability, and Operator health.

### Production caution

Do not reboot either healthy control-plane node during recovery.

---

## Q13. Two control-plane nodes are lost.

### L3 answer

This is majority/quorum loss and a disaster-recovery event. Normal controller operations cannot restore write availability. A valid same-z-stream etcd backup and documented restore procedure may be required.

### How to prove it

Freeze changes, preserve logs, verify exact release and backup/checksum/encryption information, and engage Red Hat support.

### Troubleshooting / operational approach

Communicate expected recovery point, follow the version-specific restore procedure, then validate all platform and application layers.

### Production caution

Do not improvise etcd member commands or use a backup from another z-stream.

---

## Q14. Namespace deletion is blocked by an unavailable third-party APIService.

### L3 answer

Namespace cleanup needs API discovery and finalizer controllers. An unavailable APIService can prevent enumeration and completion.

### How to prove it

Inspect namespace conditions, `oc get apiservices`, backing service/endpoints, CSV/Operator, finalizers, and remaining custom resources.

### Troubleshooting / operational approach

Restore the APIService or Operator, let cleanup finish, and force-finalize only after external-resource review.

### Production caution

Force removal can orphan third-party infrastructure and make later reinstall inconsistent.

---

## Q15. Image pulls fail only on one node.

### L3 answer

A node-local issue is likely: DNS, proxy, CA trust, time, disk, CRI-O, mirror config, or network path. Global registry health is less likely if other nodes pull successfully.

### How to prove it

Compare failed and healthy nodes: CRI-O logs, `crictl pull`, filesystem/inodes, DNS, route, certificates, proxy, and MCO current config.

### Troubleshooting / operational approach

Correct the node-specific drift or failure, validate multiple image pulls, and ensure MCP remains healthy.

### Production caution

Do not change cluster-wide registry policy for a single-node problem until comparison proves it is shared.

---

## Q16. Pods on one node cannot resolve DNS.

### L3 answer

The node-local DNS daemon path, CNI, route, MTU, conntrack, or node network can fail while cluster DNS is healthy elsewhere.

### How to prove it

Check DNS pods and placement, affected pod resolver file, direct DNS Service queries, OVN pod, node network, and compare a healthy node.

### Troubleshooting / operational approach

Restore the affected node/network component and run Service and external DNS tests before returning it to service.

### Production caution

Do not restart the entire DNS stack for one-node failure.

---

## Q17. A certificate replacement breaks console access.

### L3 answer

Identify whether ingress, OAuth, console, API, or external load balancer owns the endpoint. Common defects are key mismatch, missing intermediate, wrong SAN, malformed secret, or router rollout failure.

### How to prove it

Use `openssl s_client`, inspect ingress/authentication/console Operators, relevant secret, endpoint DNS, and router logs.

### Troubleshooting / operational approach

Restore the previous known-good customer-managed certificate if safe, correct the full chain, and validate console and OAuth callback.

### Production caution

Do not replace Operator-generated internal certificates as if they were customer-managed.

---

## Q18. Monitoring storage is full.

### L3 answer

The root may be insufficient PVC capacity, retention, high ingestion, cardinality explosion, failed compaction, or storage performance. Full monitoring storage threatens alerting and incident visibility.

### How to prove it

Inspect monitoring PVCs, TSDB size and series growth, retention configuration, Prometheus logs, cardinality, and storage health.

### Troubleshooting / operational approach

Expand through supported storage procedures if needed and fix unbounded metrics or retention. Validate targets, rules, and alerts afterward.

### Production caution

Do not manually delete Prometheus data files; that can corrupt the TSDB and erase evidence.

---

## Q19. Drain is blocked by a PDB during urgent hardware repair.

### L3 answer

The PDB is correctly protecting availability, but hardware risk may force a business tradeoff. Determine redundancy, failure probability, and whether replicas can be scaled or moved first.

### How to prove it

Inspect PDB disruptions allowed, replicas, readiness, topology, remaining capacity, and hardware severity.

### Troubleshooting / operational approach

Add capacity or replicas, recover an unhealthy replica, or obtain approved temporary PDB change. Force only when accepted risk is documented.

### Production caution

Urgency does not remove the need to understand potential application outage or data loss.

---

## Q20. Cluster looks healthy but users report intermittent slowness.

### L3 answer

Platform health conditions are broad and may miss application latency, external dependencies, load-balancer behavior, database contention, or tail latency. Define the exact transaction, percentile, location, namespace, and time.

### How to prove it

Trace client DNS/TLS, load balancer, router, Service, pod, database, and external APIs. Correlate platform metrics with application tracing and business data.

### Troubleshooting / operational approach

Compare healthy and slow requests and identify the dominant latency component before changing platform capacity.

### Production caution

Do not dismiss users because Cluster Operators are green; a healthy control plane can host a slow application.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/architecture/control-plane
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/nodes/working-with-pods
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/networking_overview/networking_overview
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/storage/storage
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/updating_clusters/performing-a-cluster-update
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/backup_and_restore/backup-restore-overview

---

<!-- Original file: 14. Automation Governance and Operational Excellence.md -->

# OpenShift Administrator L3 Interview Q&A — Automation, Governance, and Operational Excellence

> **Baseline:** Red Hat OpenShift Container Platform 4.21  
> **Level:** Senior / L3 / Corporate production administration  
> **Scope:** GitOps, drift, RBAC governance, runbooks, SLOs, audit readiness, and team maturity.

A complete L3 answer should explain architecture, show evidence, describe business impact, use a safe troubleshooting sequence, and finish with validation and rollback considerations.

---

## Q1. What should be automated and what should remain controlled?

    ### L3 answer

    Automate repeatable, tested, low-risk tasks such as namespace onboarding, policy deployment, backup verification, certificate-expiry reporting, health collection, and compliance checks. Keep destructive recovery, etcd restore, mass reboot, broad privilege, and emergency policy bypass under explicit human approval.

Automation should be idempotent, version-controlled, observable, and able to stop safely.

    ### How to prove it

    Review source control, pipeline logs, test results, service account permissions, change approvals, rollback, and failure notifications.

    ### Troubleshooting / operational approach

    Introduce automation in lower environments, add dry-run and validation, then promote. Require human gates for high-blast-radius actions.

    ### Production caution

    Retries can amplify a bad change; automation must fail safely rather than repeatedly forcing state.

---

## Q2. How do GitOps and OpenShift administration interact?

    ### L3 answer

    GitOps stores approved desired state in version control and reconciles it with a controller such as Argo CD. It improves traceability, consistency, and recovery.

Ownership boundaries are essential because Operators also reconcile resources. GitOps should manage parent configuration, not fight generated operands.

    ### How to prove it

    Inspect Git commit, Application sync/health, live diff, owner references, audit logs, and Operator-managed fields.

    ### Troubleshooting / operational approach

    During incidents, pause reconciliation only when necessary, record live divergence, fix the source, and restore Git as the authority.

    ### Production caution

    Manual changes that GitOps immediately reverts can confuse incident responders and create repeated outages.

---

## Q3. What is configuration drift?

    ### L3 answer

    Drift is a difference between approved desired configuration and live cluster or node state. It can come from manual edits, emergency SSH changes, failed sync, unmanaged scripts, or incorrect ownership.

Not every difference is drift; Operators legitimately mutate generated fields.

    ### How to prove it

    Use GitOps diff, policy engines, audit, MachineConfig state, compliance scans, and managed fields to identify unauthorized differences.

    ### Troubleshooting / operational approach

    Correct the authoritative source and reconcile. Record emergency drift and remove it after the incident.

    ### Production caution

    Blindly overwriting live state without ownership analysis can break Operator reconciliation.

---

## Q4. How do you govern RBAC at scale?

    ### L3 answer

    Use enterprise groups, a standard role catalog, namespace-scoped bindings, time-bound privilege, separation of duties, and periodic access review. Avoid direct user bindings.

High-risk permissions require special review: secrets, exec, impersonate, SCC use, rolebinding changes, node proxy, and cluster writes.

    ### How to prove it

    Export roles/bindings/groups, test with impersonation, inspect inactive access, and record owner/expiry.

    ### Troubleshooting / operational approach

    Automate request, approval, provisioning, and expiry while preserving audit evidence.

    ### Production caution

    A role with wildcard resources/verbs often grants more than its friendly name suggests.

---

## Q5. What belongs in a production runbook?

    ### L3 answer

    A runbook needs purpose, scope, prerequisites, exact commands, expected outputs, decision points, impact, rollback, validation, evidence, escalation, owner, and version applicability.

Commands without interpretation are not enough for L3 operations.

    ### How to prove it

    Review last test date, target OpenShift version, placeholders, dependencies, access requirements, and recovery path.

    ### Troubleshooting / operational approach

    Have a different engineer execute it in a lab or lower environment and update unclear steps.

    ### Production caution

    Never embed production secrets or assume undocumented tribal knowledge.

---

## Q6. How do you manage cluster-wide changes?

    ### L3 answer

    Cluster-wide resources such as OAuth, proxy, network, DNS, ingress, API server, scheduler, image config, and MachineConfig have large blast radius. Use peer review, testing, backup, maintenance planning, staged rollout, and rollback.

Understand which Operator owns the resource and how reconciliation occurs.

    ### How to prove it

    Capture before/after YAML, Operator and canary health, events, alerts, and change timeline.

    ### Troubleshooting / operational approach

    Make one logical change, observe convergence, validate platform and business transactions, then close.

    ### Production caution

    Multiple simultaneous cluster-wide edits make causality and rollback uncertain.

---

## Q7. How do you define platform SLOs?

    ### L3 answer

    Define measurable indicators for API availability/latency, scheduling, DNS, ingress, image pull, storage provisioning, and incident response. Targets should align with business needs and architecture.

Use error budgets to prioritize reliability work and define maintenance treatment.

    ### How to prove it

    Use objective metrics, clearly defined windows, exclusions, ownership, and alert thresholds. Compare actual performance with target.

    ### Troubleshooting / operational approach

    Review breaches, recurring causes, and investment needed in capacity, automation, or design.

    ### Production caution

    An SLO must not be a vague statement such as “cluster should always be up.”

---

## Q8. How do you prepare for compliance audit?

    ### L3 answer

    Maintain version and asset inventory, access reviews, audit evidence, encryption status, certificate inventory, vulnerability/compliance scans, exceptions, change records, backup/restore results, and incident records.

Prove that controls operate in practice, not only that a policy document exists.

    ### How to prove it

    Collect current reports with timestamps, owners, remediation status, approvals, and samples of control execution.

    ### Troubleshooting / operational approach

    Close evidence gaps, revoke stale privilege, test recovery, and protect exported data.

    ### Production caution

    Do not give auditors broad cluster-admin access when scoped evidence can satisfy the requirement.

---

## Q9. What belongs in a daily operational review?

    ### L3 answer

    Review ClusterVersion/Operators, nodes/MCPs, critical alerts, failed or pending pods, storage and certificate thresholds, backup status, recent changes, security events, capacity trends, and open incidents.

Automation can collect evidence, but a human must interpret anomalies.

    ### How to prove it

    Use a standard dashboard/report with exceptions, owners, due dates, and links to incidents or changes.

    ### Troubleshooting / operational approach

    Escalate recurring warnings and trend problems before they become outages.

    ### Production caution

    A screenshot-only review without actions and ownership does not improve reliability.

---

## Q10. How does an L3 administrator mentor L1/L2 safely?

    ### L3 answer

    Provide labs, read-only access, runbooks, shadowing, peer review, and progressively scoped permissions. Teach evidence collection, failure domains, and escalation thresholds before remediation.

Use incident simulations and post-incident reviews to build reasoning, not command memorization.

    ### How to prove it

    Track competency through practical scenarios, reviewed changes, and ability to explain impact and rollback.

    ### Troubleshooting / operational approach

    Delegate low-risk tasks with guardrails and increase scope after consistent safe performance.

    ### Production caution

    Production cluster-admin should not be the training environment.

---

## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/overview/welcome-index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/operators/index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/authentication_and_authorization/understanding-authentication

---

<!-- Original file: 15. Command Based Interview Lab.md -->

# OpenShift Administrator L3 — Command-Based Interview Lab

> **Baseline:** OpenShift Container Platform 4.21  
> These are evidence-gathering examples. Replace placeholders and evaluate production impact before execution.

## 1. Cluster version and Cluster Operators

```bash
oc get clusterversion
oc describe clusterversion version
oc adm upgrade
oc get clusteroperators
oc get co -o custom-columns='NAME:.metadata.name,AVAILABLE:.status.conditions[?(@.type=="Available")].status,PROGRESSING:.status.conditions[?(@.type=="Progressing")].status,DEGRADED:.status.conditions[?(@.type=="Degraded")].status'
```

**Expected interview explanation:** Identify the first Operator that changed state, read its reason/message, distinguish planned Progressing from failure, and state the affected capability.

## 2. API health and discovery

```bash
oc get --raw='/readyz?verbose'
oc get --raw='/livez?verbose'
oc api-resources
oc api-versions
oc get apirequestcounts
```

**Expected interview explanation:** `readyz` indicates readiness to serve traffic; `livez` indicates process liveness. APIRequestCount helps identify clients still using deprecated APIs.

## 3. Nodes and MachineConfig

```bash
oc get nodes -o wide
oc describe node <node>
oc get mcp
oc describe mcp worker
oc get mc
oc get nodes -o custom-columns='NAME:.metadata.name,CURRENT:.metadata.annotations.machineconfiguration\.openshift\.io/currentConfig,DESIRED:.metadata.annotations.machineconfiguration\.openshift\.io/desiredConfig,STATE:.metadata.annotations.machineconfiguration\.openshift\.io/state'
```

**Expected interview explanation:** Correlate Node conditions and taints with the current and desired rendered MachineConfig and pool convergence.

## 4. Workloads and events

```bash
oc get pods -A -o wide
oc get pods -A --field-selector=status.phase!=Running,status.phase!=Succeeded
oc describe pod <pod> -n <namespace>
oc logs <pod> -n <namespace> --all-containers
oc logs <pod> -n <namespace> --previous
oc get events -A --sort-by=.lastTimestamp
```

**Expected interview explanation:** Events normally identify scheduling, image, mount, sandbox, admission, probe, or eviction problems. Previous logs are essential after a container restart.

## 5. Scheduling and disruption

```bash
oc get pdb -A
oc get quota,limitrange -A
oc adm cordon <node>
oc adm drain <node> --ignore-daemonsets --delete-emptydir-data=false
oc adm uncordon <node>
```

**Expected interview explanation:** Review PDB, local data, replicas, volume behavior, affinity, and remaining cluster capacity before drain.

## 6. Networking and ingress

```bash
oc get co network ingress dns
oc get pods -n openshift-ovn-kubernetes -o wide
oc get pods -n openshift-ingress -o wide
oc get ingresscontroller -n openshift-ingress-operator
oc get route -A
oc get svc,endpointslices -n <namespace>
oc get networkpolicy -A
```

**Expected interview explanation:** Trace DNS → load balancer → router → Route → Service → EndpointSlice → ready pod.

## 7. DNS

```bash
oc exec -n <namespace> <pod> -- cat /etc/resolv.conf
oc exec -n <namespace> <pod> -- getent hosts <service>.<namespace>.svc.cluster.local
oc exec -n <namespace> <pod> -- dig <external-name>
```

**Expected interview explanation:** Compare affected and healthy namespaces/nodes. Separate Service discovery failure from external resolver failure.

## 8. Storage

```bash
oc get storageclass
oc get pvc,pv -A
oc describe pvc <pvc> -n <namespace>
oc get volumeattachments
oc get volumesnapshot,volumesnapshotcontent -A
```

**Expected interview explanation:** Identify provisioning, binding, topology, attachment, mount, filesystem, or application failure.

## 9. Authentication and RBAC

```bash
oc whoami
oc get oauth cluster -o yaml
oc get users
oc get identities
oc get groups
oc auth can-i --list -n <namespace> --as=<user>
oc adm policy who-can <verb> <resource> -n <namespace>
```

**Expected interview explanation:** Separate identity-provider authentication, user/identity mapping, RBAC authorization, and SCC/admission.

## 10. Security Context Constraints

```bash
oc get scc
oc auth can-i use scc/<scc> --as=system:serviceaccount:<namespace>:<serviceaccount>
oc adm policy who-can use scc/<scc>
```

**Expected interview explanation:** Determine the least-privileged SCC that can admit the workload. Prefer correcting the image and security context over granting privileged.

## 11. Operator Lifecycle Manager

```bash
oc get catalogsource -A
oc get subscription,installplan,csv,operatorgroup -A
oc describe subscription <name> -n <namespace>
oc describe installplan <name> -n <namespace>
oc describe csv <name> -n <namespace>
```

**Expected interview explanation:** Follow CatalogSource → Subscription → InstallPlan → CSV → Operator deployment → operand.

## 12. Host-level node debug

```bash
oc debug node/<node>
chroot /host
systemctl status kubelet crio
journalctl -u kubelet -u crio --since "1 hour ago"
df -h
df -i
free -m
ip addr
ip route
ss -lntp
crictl ps -a
```

**Expected interview explanation:** Use host access to collect evidence. Avoid unmanaged persistent modification to RHCOS.

## 13. Support and evidence

```bash
oc adm must-gather --dest-dir=/secure/path
oc adm inspect ns/<namespace>
```

**Expected interview explanation:** Collect while the problem is present, protect sensitive output, and supplement with external load-balancer, storage, identity-provider, and application evidence.

## 14. High-value quick checks

```bash
oc get csr
oc get apiservices
oc get validatingwebhookconfigurations,mutatingwebhookconfigurations
oc adm top nodes
oc adm top pods -A --containers
oc get pods -A --sort-by=.status.startTime
```

## 15. Commands that require extra caution

```bash
oc adm drain <node> --force
oc delete crd <name>
oc patch namespace <name> --type=merge -p '{"metadata":{"finalizers":[]}}'
oc delete machine <name> -n openshift-machine-api
```

These commands can evict unmanaged pods, delete all custom resources of a type, orphan external resources, or destroy infrastructure. In an L3 interview, explain prerequisites, impact, approval, rollback/recovery, and validation before using them.


## Official Red Hat references

- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/overview/welcome-index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/nodes/working-with-pods
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/networking_overview/networking_overview
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/storage/storage
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/authentication_and_authorization/understanding-authentication
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html-single/operators/index
- https://docs.redhat.com/en/documentation/openshift_container_platform/4.21/html/support/gathering-cluster-data

---

<!-- Original file: 16. Mock Interview and Scoring Sheet.md -->

# OpenShift Administrator L3 — Mock Interview and Scoring Sheet

## Scoring model

Score each answer from **0 to 5**:

- **0:** No answer or unsafe answer
- **1:** Definition only
- **2:** Basic command knowledge
- **3:** Correct operational sequence
- **4:** Includes impact, evidence, remediation, rollback, and validation
- **5:** Adds architecture, failure domains, prevention, governance, and clear communication

A strong L3 candidate should average **4 or above** and must not give unsafe answers for etcd, control-plane quorum, storage fencing, SCC, MachineConfig, or update recovery.

## Round 1 — Architecture

1. Explain how an API request becomes a running pod.
2. How do CVO, Cluster Operators, and MCO cooperate during an update?
3. Why is etcd storage latency important?
4. What is the difference between a Cluster Operator and an OLM Operator?
5. What does reconciliation mean during troubleshooting?

## Round 2 — Nodes and scheduling

6. A node is NotReady. Show your first ten minutes.
7. A drain is blocked by a PDB. What decisions are required?
8. Why is a pod Pending when the cluster has free resources?
9. How do you replace a failed Machine API-managed worker?
10. Explain OOMKilled versus node eviction.

## Round 3 — Networking and storage

11. Trace an external HTTPS request to a pod.
12. A Route returns 503. What do you check?
13. Only cross-node traffic fails. What is your approach?
14. A PVC is Pending under WaitForFirstConsumer. Explain.
15. A volume reports Multi-Attach after node loss. What is the safety concern?

## Round 4 — Security and Operators

16. Authentication works but the user gets Forbidden.
17. Explain SCC admission and least privilege.
18. A CSV is Failed. Trace the OLM resources.
19. An admission webhook is down and blocks deployments.
20. How do you rotate a customer-managed ingress certificate?

## Round 5 — Updates and DR

21. List update prechecks and stop criteria.
22. What does Upgradeable=False mean?
23. Can an etcd backup roll back a minor update?
24. One etcd member is down. What do you do?
25. Two control-plane nodes are lost. What do you do?

## Round 6 — Corporate scenarios

26. API latency spikes at the same time nightly.
27. Monitoring storage is full.
28. Image pulls fail only on one worker.
29. All Routes fail after a firewall change.
30. Cluster Operators are green but users report slowness.

## Mandatory red-flag answers

Reject or heavily downgrade an answer that recommends any of the following without documented prerequisites:

- Reboot all control-plane nodes.
- Delete etcd data or restore etcd while the API is usable.
- Approve every CSR automatically.
- Grant `cluster-admin` or `privileged` to bypass diagnosis.
- Delete a CRD to repair an Operator.
- Remove namespace finalizers without checking external resources.
- Force-detach an RWO volume before fencing the old node.
- Edit rendered MachineConfig or MCO status.
- Start an update from an unexplained degraded cluster.
- Perform multiple simultaneous changes during an incident.

## Candidate self-review template

For each weak answer, write:

1. **Component architecture**
2. **Failure impact and scope**
3. **First evidence commands**
4. **Likely causes**
5. **Safe remediation**
6. **Rollback or recovery**
7. **Validation**
8. **Preventive control**
