# Harvester and Rancher - Corporate L3 - 500 Interview Questions and Answers

> Git-compatible compact edition.  
> Exactly 500 questions across 25 domains.  
> UTF-8, Unix line endings, balanced Markdown fences, and a simple ASCII filename.

## L3 Answer Framework

For each question:

1. Identify the Harvester, Rancher, Fleet, KubeVirt, Longhorn, RKE2/K3s, or Kubernetes resource that owns desired state.
2. Trace reconciliation through controllers, agents, nodes, VMs, storage, networking, and external services.
3. Check version compatibility, credentials, certificates, capacity, and failure domains.
4. Use read-only commands first and preserve conditions, events, logs, and recent changes.
5. Apply the smallest reversible recovery and validate the real workload transaction.
6. Correct the automation, Git, Helm, Fleet, Terraform, or documented source of truth.

## Table of Contents

- [01. Harvester and Rancher Architecture, Components, and Operating Model](#01-harvester-and-rancher-architecture-components-and-operating-model)
- [02. Harvester Installation, Hardware, Networking Prerequisites, and Bootstrapping](#02-harvester-installation-hardware-networking-prerequisites-and-bootstrapping)
- [03. Harvester Nodes, Maintenance, Scheduling, and Capacity](#03-harvester-nodes-maintenance-scheduling-and-capacity)
- [04. Harvester Virtual Machines, KubeVirt, and Lifecycle Management](#04-harvester-virtual-machines-kubevirt-and-lifecycle-management)
- [05. Harvester Images, Volumes, ISO Boot, and VM Templates](#05-harvester-images-volumes-iso-boot-and-vm-templates)
- [06. Harvester Storage, Longhorn Architecture, Replication, and Performance](#06-harvester-storage-longhorn-architecture-replication-and-performance)
- [07. Harvester Networking, VLANs, Bridges, and VM Connectivity](#07-harvester-networking-vlans-bridges-and-vm-connectivity)
- [08. Harvester VM Migration, High Availability, and Failure Recovery](#08-harvester-vm-migration-high-availability-and-failure-recovery)
- [09. Harvester Backups, Snapshots, Restore, and Disaster Recovery](#09-harvester-backups-snapshots-restore-and-disaster-recovery)
- [10. Harvester Upgrades, Air-Gapped Operations, and Lifecycle Management](#10-harvester-upgrades-air-gapped-operations-and-lifecycle-management)
- [11. Rancher Installation, High Availability, TLS, and Load Balancing](#11-rancher-installation-high-availability-tls-and-load-balancing)
- [12. Rancher Cluster Provisioning, RKE2, K3s, and Imported Clusters](#12-rancher-cluster-provisioning-rke2-k3s-and-imported-clusters)
- [13. Rancher Authentication, Authorization, Projects, and Multi-Tenancy](#13-rancher-authentication-authorization-projects-and-multi-tenancy)
- [14. Rancher Fleet, GitOps, Continuous Delivery, and Bundles](#14-rancher-fleet-gitops-continuous-delivery-and-bundles)
- [15. Rancher–Harvester Integration and Virtualization Management](#15-rancherharvester-integration-and-virtualization-management)
- [16. Guest Kubernetes Clusters on Harvester, Cloud Provider, CSI, and Load Balancers](#16-guest-kubernetes-clusters-on-harvester-cloud-provider-csi-and-load-balancers)
- [17. Monitoring, Alerting, Metrics, and Capacity Management](#17-monitoring-alerting-metrics-and-capacity-management)
- [18. Logging, Audit, Event Collection, and Troubleshooting Evidence](#18-logging-audit-event-collection-and-troubleshooting-evidence)
- [19. Security Hardening, Certificates, RBAC, and Supply Chain](#19-security-hardening-certificates-rbac-and-supply-chain)
- [20. Rancher Backup, Restore, Migration, and Management-Plane DR](#20-rancher-backup-restore-migration-and-management-plane-dr)
- [21. Rancher Upgrades, Kubernetes Compatibility, and Lifecycle Management](#21-rancher-upgrades-kubernetes-compatibility-and-lifecycle-management)
- [22. Automation, APIs, Terraform, GitOps, and Operational Tooling](#22-automation-apis-terraform-gitops-and-operational-tooling)
- [23. Performance, Scale, Resource Limits, and Troubleshooting Methodology](#23-performance-scale-resource-limits-and-troubleshooting-methodology)
- [24. Rancher Apps, Helm Charts, Catalogs, and Cluster Tools](#24-rancher-apps-helm-charts-catalogs-and-cluster-tools)
- [25. Corporate L3 Harvester and Rancher Incident Scenarios](#25-corporate-l3-harvester-and-rancher-incident-scenarios)

---

# 01. Harvester and Rancher Architecture, Components, and Operating Model

## Q001. Explain Harvester architecture and its major components.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q002. How do Harvester, KubeVirt, Longhorn, RKE2, and Rancher integrate?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q003. Explain Rancher Manager architecture and its major components.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q004. How does Rancher manage downstream Kubernetes clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q005. How does Harvester differ from a traditional virtualization platform?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q006. How does Rancher differ from a Kubernetes distribution?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get nodes -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q007. How do Harvester management clusters and guest clusters differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get nodes -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q008. How do Rancher local and downstream clusters differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q009. How do Kubernetes controllers reconcile Harvester virtual machines?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q010. How do Rancher controllers reconcile provisioning resources?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q011. How does the Harvester UI interact with Kubernetes APIs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q012. How does the Rancher UI interact with management and downstream APIs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q013. How do cattle-system, cattle-fleet-system, and Harvester namespaces differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q014. How do CRDs shape Harvester and Rancher operations?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q015. How do control-plane and data-plane responsibilities differ?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **operate Harvester HCI and Rancher multi-cluster management as controller-driven Kubernetes platforms**.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A -o wide
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q016. How do failure domains influence Harvester and Rancher architecture?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q017. How do you assess overall platform health before a change?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Harvester HCI and Rancher multi-cluster management as controller-driven Kubernetes platforms**.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q018. How do you identify the owning controller for a failed operation?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Harvester HCI and Rancher multi-cluster management as controller-driven Kubernetes platforms**.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q019. How do you collect evidence for SUSE or Rancher support?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

## Q020. What are the responsibilities of a Corporate/L3 Harvester and Rancher administrator?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'harvester|kubevirt|longhorn|cattle|fleet'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include confusing UI state with reconciliation, editing controller-owned objects, hidden dependencies, weak evidence, and unclear ownership. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user or API request → custom resource → Rancher, Fleet, Harvester, KubeVirt, Longhorn, or Kubernetes controller → node, VM, volume, cluster, or agent action → status conditions** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability,...

---

# 02. Harvester Installation, Hardware, Networking Prerequisites, and Bootstrapping

## Q021. What hardware requirements should be validated before installing Harvester?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q022. How do CPU virtualization extensions affect Harvester?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q023. How do BIOS, UEFI, Secure Boot, and IOMMU settings affect installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q024. How do you plan management, storage, migration, and VM networks?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
ip addr; ip route
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q025. How do NIC count and bandwidth affect Harvester design?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q026. How do disk type, endurance, latency, and capacity affect Longhorn?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q027. How do you install the first Harvester node?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
ip addr; ip route
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q028. How do additional nodes join a Harvester cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q029. How does the management VIP work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q030. How do DHCP and static network configurations differ during installation?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q031. How do you install Harvester in an air-gapped environment?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q032. How do you customize the Harvester configuration file?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q033. How do you validate DNS and NTP before installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q034. How do you plan hostnames and node identity?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher role templates connect global, cluster, and project access to Kubernetes RBAC.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
ip addr; ip route
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q035. How do you troubleshoot a Harvester installer that cannot see disks?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q036. How do you troubleshoot installer network failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q037. How do you troubleshoot a node that fails to join the cluster?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q038. How do you recover from a partially completed installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **install Harvester on validated bare-metal infrastructure**.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
ip addr; ip route
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q039. How do you validate a new Harvester cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
timedatectl; resolvectl status
lsblk -o NAME,SIZE,TYPE,MODEL,ROTA
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q040. How do you create a production Harvester installation runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip addr; ip route
timedatectl; resolvectl status
```

**Key risks:** Typical risks include unsupported hardware, disk latency, DNS/NTP failure, incorrect NIC mapping, VIP conflict, and partial bootstrap. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **hardware and firmware validation → network, DNS, NTP, disk preparation → first-node bootstrap → VIP and RKE2 services → additional-node join → validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 03. Harvester Nodes, Maintenance, Scheduling, and Capacity

## Q041. How are Harvester nodes represented in Kubernetes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q042. How do node roles affect Harvester behavior?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl get events -A --field-selector involvedObject.kind=Node
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q043. How do labels, taints, and tolerations affect VM placement?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q044. How do CPU and memory requests affect virtual-machine scheduling?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl get events -A --field-selector involvedObject.kind=Node
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q045. How do overcommit settings affect capacity planning?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --field-selector involvedObject.kind=Node
kubectl get nodes -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q046. How do you cordon and drain a Harvester node?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q047. How do you enter and exit node maintenance mode?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe node <node>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q048. How does live migration interact with maintenance mode?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl get events -A --field-selector involvedObject.kind=Node
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q049. How do PodDisruptionBudgets affect node maintenance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --field-selector involvedObject.kind=Node
kubectl get nodes -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q050. How do you safely reboot a Harvester node?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q051. How do you replace a failed Harvester node?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --field-selector involvedObject.kind=Node
kubectl get nodes -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q052. How do you add capacity to a Harvester cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl get events -A --field-selector involvedObject.kind=Node
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q053. How do you remove a node safely?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --field-selector involvedObject.kind=Node
kubectl get nodes -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q054. How do you identify resource imbalance between nodes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl get events -A --field-selector involvedObject.kind=Node
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q055. How does the VM Auto Balance add-on work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe node <node>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q056. How do you troubleshoot a VM that cannot be scheduled?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes -o wide
kubectl describe node <node>
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q057. How do you troubleshoot a node stuck NotReady?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe node <node>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q058. How do you troubleshoot node pressure conditions?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --field-selector involvedObject.kind=Node
kubectl get nodes -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q059. How do you verify capacity after node changes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe node <node>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q060. How do you design node-maintenance and capacity runbooks?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **manage Harvester nodes, maintenance, placement, and capacity**.

**Controller flow:** `node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe node <node>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include insufficient evacuation capacity, PDB blockage, node pressure, unschedulable VMs, and unsafe removal. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **node capacity and labels → VM scheduling → cordon, drain, migration, or maintenance → node servicing or replacement → capacity revalidation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 04. Harvester Virtual Machines, KubeVirt, and Lifecycle Management

## Q061. Explain how KubeVirt implements virtual machines on Kubernetes.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. KubeVirt represents persistent VM configuration with VirtualMachine and the active guest with VirtualMachineInstance; virt-launcher runs the guest process.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe vm <vm> -n <namespace>
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q062. How do VirtualMachine and VirtualMachineInstance resources differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
virtctl console <vm> -n <namespace>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q063. How do virt-launcher Pods relate to virtual machines?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe vm <vm> -n <namespace>
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q064. How do VM start, stop, restart, pause, and migrate operations work?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl describe vm <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q065. How do VM templates improve standardization?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl describe vm <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q066. How do cloud-init and ignition configure guest operating systems?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q067. How do you inject SSH keys into a VM?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
virtctl console <vm> -n <namespace>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q068. How do you configure CPU, memory, disks, and interfaces?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q069. How do dedicated CPU and CPU pinning affect workloads?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe vm <vm> -n <namespace>
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q070. How do huge pages affect VM performance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q071. How do UEFI and secure-boot options affect VMs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q072. How do guest agents improve VM management?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
virtctl console <vm> -n <namespace>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q073. How do VM affinity and anti-affinity rules work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vm,vmi -A -o wide
kubectl describe vm <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q074. How do you clone a virtual machine?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q075. How do you export or import a virtual machine?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
virtctl console <vm> -n <namespace>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q076. How do you troubleshoot a VM stuck Starting?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe vm <vm> -n <namespace>
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q077. How do you troubleshoot a VM that repeatedly shuts down?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q078. How do you troubleshoot virt-launcher failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
virtctl console <vm> -n <namespace>
kubectl get vm,vmi -A -o wide
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q079. How do you diagnose poor VM performance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe vm <vm> -n <namespace>
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q080. How do you design enterprise VM lifecycle standards?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **operate KubeVirt virtual-machine lifecycle and guest configuration**.

**Controller flow:** `VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n <namespace> <virt-launcher-pod> -c compute --since=30m
virtctl console <vm> -n <namespace>
```

**Key risks:** Typical risks include bad cloud-init, unschedulable VMs, missing guest agent, resource overcommit, and virt-launcher failure. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **VirtualMachine desired state → VirtualMachineInstance → virt-controller and virt-handler → virt-launcher Pod and libvirt → guest state** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 05. Harvester Images, Volumes, ISO Boot, and VM Templates

## Q081. How does Harvester manage VM images?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,pv -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q082. How do HTTP, S3, and local image sources differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q083. How do image download and import workflows work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q084. How do you validate image checksums and provenance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineimages.harvesterhci.io -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q085. How do you manage operating-system ISO images?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q086. How do you create a bootable VM from an ISO?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineimages.harvesterhci.io -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q087. How do Longhorn volumes back VM disks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q088. How do raw and qcow2 image formats affect import?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineimages.harvesterhci.io -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q089. How do you resize a VM volume?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q090. How do you attach and detach additional disks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,pv -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q091. How do volume access modes affect VMs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineimages.harvesterhci.io -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q092. How do you create reusable VM templates?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q093. How do you version templates and images?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q094. How do you manage golden images?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q095. How do you patch a golden image safely?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q096. How do you troubleshoot an image download stuck in progress?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,pv -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q097. How do you troubleshoot image checksum or format failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q098. How do you troubleshoot a volume attachment failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineimage <image> -n <namespace>
kubectl get pvc,pv -A
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q099. How do you recover an accidentally deleted VM disk?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,pv -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q100. How do you design enterprise image and template governance?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **govern VM images, volumes, ISO media, and reusable templates**.

**Controller flow:** `trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineimages.harvesterhci.io -A
kubectl describe virtualmachineimage <image> -n <namespace>
```

**Key risks:** Typical risks include untrusted or corrupt images, format mismatch, stalled imports, detached disks, and golden-image drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **trusted image source → download and checksum → image or backing volume → template → VM disk attachment and boot** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 06. Harvester Storage, Longhorn Architecture, Replication, and Performance

## Q101. Explain Longhorn architecture in Harvester.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get pods -o wide
kubectl -n longhorn-system get volumes.longhorn.io,engines.longhorn.io,replicas.longhorn.io
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q102. How do Longhorn managers, engines, replicas, and instance managers interact?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q103. How does synchronous replica storage work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q104. How do replica counts affect availability and capacity?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q105. How do storage classes and volume policies work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q106. How do disk tags and node tags affect replica placement?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get volumes.longhorn.io,engines.longhorn.io,replicas.longhorn.io
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q107. How do storage networks affect Longhorn performance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q108. How do thin provisioning and overprovisioning affect risk?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q109. How do data locality settings affect VM workloads?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q110. How do snapshots differ from backups in Longhorn?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get volumes.longhorn.io,engines.longhorn.io,replicas.longhorn.io
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q111. How do recurring jobs work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q112. How do you monitor Longhorn volume health?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q113. How do you identify degraded or faulted replicas?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get volumes.longhorn.io,engines.longhorn.io,replicas.longhorn.io
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q114. How do you troubleshoot a volume stuck Detached?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q115. How do you troubleshoot a volume stuck Attaching?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q116. How do you troubleshoot replica rebuild failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q117. How do you troubleshoot high storage latency?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q118. How do you recover from a failed storage disk?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
iostat -xz 1 5
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q119. How do you plan storage capacity and headroom?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **operate Longhorn distributed block storage with capacity and failure-domain awareness**.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
iostat -xz 1 5
kubectl -n longhorn-system get pods -o wide
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q120. How do you create a Longhorn L3 troubleshooting workflow?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get volumes.longhorn.io,engines.longhorn.io,replicas.longhorn.io
kubectl -n longhorn-system logs -l app=longhorn-manager --since=30m
```

**Key risks:** Typical risks include degraded replicas, overprovisioning, rebuild storms, storage-network latency, disk failure, and insufficient free space. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **PVC or VM volume → Longhorn engine → synchronous replicas on selected disks and nodes → health, rebuild, snapshot, or backup** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 07. Harvester Networking, VLANs, Bridges, and VM Connectivity

## Q121. Explain Harvester management and VM network architecture.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusternetworks.harvesterhci.io -A
kubectl get networkconfigs.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q122. How do VLAN and untagged VM networks work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get network-attachment-definitions -A
ip -d link; bridge vlan show
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q123. How do cluster networks and network configurations relate?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip -d link; bridge vlan show
kubectl get clusternetworks.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q124. How do Linux bridges participate in VM networking?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusternetworks.harvesterhci.io -A
kubectl get networkconfigs.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q125. How do Multus and CNI resources affect VMs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip -d link; bridge vlan show
kubectl get clusternetworks.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q126. How do you create a VLAN-backed VM network?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get network-attachment-definitions -A
ip -d link; bridge vlan show
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q127. How do you map physical NICs to VM networks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip -d link; bridge vlan show
kubectl get clusternetworks.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q128. How do bond modes affect availability and performance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q129. How do MTU settings affect VM traffic?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q130. How do you provide multiple NICs to a VM?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q131. How do you assign static IP addresses to VMs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q132. How do DHCP servers interact with VM networks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get network-attachment-definitions -A
ip -d link; bridge vlan show
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q133. How do network policies affect Harvester system components?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusternetworks.harvesterhci.io -A
kubectl get networkconfigs.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q134. How do you troubleshoot a VM with no network connectivity?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q135. How do you troubleshoot VLAN tagging problems?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip -d link; bridge vlan show
kubectl get clusternetworks.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q136. How do you troubleshoot asymmetric routing?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
ip -d link; bridge vlan show
kubectl get clusternetworks.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q137. How do you troubleshoot duplicate IP addresses?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get network-attachment-definitions -A
ip -d link; bridge vlan show
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q138. How do you capture VM network traffic?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get network-attachment-definitions -A
ip -d link; bridge vlan show
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q139. How do you change a production network safely?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide resilient management and VM networking with VLAN and bond controls**.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkconfigs.harvesterhci.io -A
kubectl get network-attachment-definitions -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q140. How do you design Harvester network segmentation standards?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusternetworks.harvesterhci.io -A
kubectl get networkconfigs.harvesterhci.io -A
```

**Key risks:** Typical risks include VLAN mismatch, wrong uplink, MTU inconsistency, duplicate IP, asymmetric routing, and management-path loss. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **physical NIC or bond → Harvester cluster network and configuration → bridge and Multus attachment → VM interface → external network** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 08. Harvester VM Migration, High Availability, and Failure Recovery

## Q141. How does live migration work in KubeVirt and Harvester?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q142. What prerequisites must be met for live migration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vmi -A -o wide
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q143. How do shared storage and network continuity affect migration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vmi -A -o wide
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q144. How does eviction strategy affect virtual machines?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vmi -A -o wide
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q145. How do you trigger a manual VM migration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineinstancemigrations -A
kubectl get vmi -A -o wide
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q146. How do you monitor migration progress?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q147. How do you troubleshoot a migration stuck Pending?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineinstancemigrations -A
kubectl get vmi -A -o wide
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q148. How do you troubleshoot a migration that fails midway?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get vmi -A -o wide
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q149. How do node failures affect running VMs?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q150. How does Harvester restart VMs after node failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q151. How do anti-affinity and topology improve VM availability?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q152. How do you test VM failover safely?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q153. How do you handle single-replica VM volumes during node failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q154. How do you recover VMs after storage degradation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q155. How do you plan maintenance without workload outage?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q156. How do you define VM RTO and RPO?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q157. How do you validate application health after VM migration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl describe virtualmachineinstancemigration <name> -n <namespace>
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q158. How do you prevent migration storms?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q159. How do you conduct a Harvester failure-domain exercise?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachineinstancemigrations -A
kubectl get vmi -A -o wide
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q160. How do you create an enterprise VM HA runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **maintain VM availability through live migration and node-failure recovery**.

**Controller flow:** `maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachineinstancemigrations -A
```

**Key risks:** Typical risks include migration storms, blocked destination capacity, incompatible CPU, network interruption, and degraded storage. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **maintenance, eviction, or failure → migration or restart decision → destination scheduling → storage and network continuity → workload validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 09. Harvester Backups, Snapshots, Restore, and Disaster Recovery

## Q161. How do Harvester VM snapshots work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q162. How do VM backups differ from snapshots?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q163. How do NFS and S3 backup targets work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinerestores -A
kubectl -n longhorn-system get backuptargets.longhorn.io
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q164. How do you configure a Harvester backup target?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q165. How do you protect backup credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q166. How do you create a manual VM backup?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinebackups -A
kubectl get virtualmachinerestores -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q167. How do you schedule recurring VM backups?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q168. How do you restore a VM in the same cluster?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **protect and restore Harvester VMs, volumes, and application data**.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinerestores -A
kubectl -n longhorn-system get backuptargets.longhorn.io
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q169. How do you restore a VM into another cluster?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **protect and restore Harvester VMs, volumes, and application data**.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q170. How do you restore only data rather than the original VM identity?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher role templates connect global, cluster, and project access to Kubernetes RBAC.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q171. How do you validate application consistency during backup?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q172. How do you troubleshoot a backup stuck Pending?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q173. How do you troubleshoot backup target connectivity?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q174. How do you troubleshoot a failed restore?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **protect and restore Harvester VMs, volumes, and application data**.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinebackups -A
kubectl get virtualmachinerestores -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q175. How do encryption keys affect backup recovery?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n longhorn-system get backuptargets.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q176. How do you protect backups from ransomware?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q177. How do you test backup retention and expiration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q178. How do you measure achieved RTO and RPO?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **protect and restore Harvester VMs, volumes, and application data**.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinerestores -A
kubectl -n longhorn-system get backuptargets.longhorn.io
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q179. How do you recover after accidental VM deletion?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **protect and restore Harvester VMs, volumes, and application data**.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get virtualmachinebackups -A
kubectl get virtualmachinerestores -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q180. How do you create a Harvester disaster-recovery runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get virtualmachinebackups -A
```

**Key risks:** Typical risks include crash-consistent databases, target or credential failure, lost encryption keys, incomplete retention, and untested restoration. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **snapshot or backup request → optional guest quiesce → volume snapshot and external transfer → NFS or S3 target → restore and application validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 10. Harvester Upgrades, Air-Gapped Operations, and Lifecycle Management

## Q181. How do Harvester upgrade paths work?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q182. Why are Harvester minor-version upgrade jumps constrained?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get upgrades.harvesterhci.io -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q183. How do underlying RKE2 and Kubernetes versions affect upgrades?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get managedcharts -A
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q184. How do you perform pre-upgrade health checks?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q185. How do you prepare an air-gapped Harvester upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get upgrades.harvesterhci.io -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q186. How do you download and stage upgrade artifacts?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q187. How do you start and monitor a Harvester upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get managedcharts -A
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q188. How are nodes upgraded sequentially?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get upgrades.harvesterhci.io -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q189. How do maintenance mode and VM migration affect upgrades?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q190. How do PodDisruptionBudgets block upgrades?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q191. How do you troubleshoot an upgrade stuck pre-draining?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get managedcharts -A
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q192. How do you troubleshoot an upgrade stuck post-draining?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get managedcharts -A
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q193. How do you troubleshoot system-service upgrade failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q194. How do you handle custom certificate issues during upgrades?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q195. How do you recover after an interrupted upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get upgrades.harvesterhci.io -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q196. How do you validate platform health after an upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get upgrades.harvesterhci.io -A
kubectl get managedcharts -A
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q197. How do you test VM and storage behavior after upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q198. How do you plan rollback when direct downgrade is unsupported?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **upgrade Harvester through supported sequential and air-gapped workflows**.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q199. How do you govern add-on compatibility during upgrades?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get managedcharts -A
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q200. How do you create a Harvester lifecycle-management standard?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include unsupported version jump, PDB blockage, partial node state, certificate failure, add-on incompatibility, and no direct downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **support-matrix and health prechecks → artifact staging → upgrade controller → node drain and VM migration → component and node upgrade → postchecks** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 11. Rancher Installation, High Availability, TLS, and Load Balancing

## Q201. How do you install Rancher on a dedicated Kubernetes cluster?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
curl -vk https://rancher.example.com/ping
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q202. Why is a highly available Helm installation preferred for production?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **install and operate an HA Rancher management plane**.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q203. How do RKE2 and K3s host-cluster options differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **install and operate an HA Rancher management plane**.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q204. How do you size the Rancher management cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
curl -vk https://rancher.example.com/ping
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q205. How do you configure DNS and load balancing for Rancher?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q206. How do Rancher-generated and externally signed certificates differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q207. How do private CA certificates integrate with Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q208. How does TLS termination affect Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q209. How do proxies and no_proxy settings affect Rancher installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q210. How do you install Rancher in an air-gapped environment?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
curl -vk https://rancher.example.com/ping
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q211. How do system-default-registry settings work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **install and operate an HA Rancher management plane**.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q212. How do you configure the Rancher Helm chart?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q213. How do you rotate Rancher TLS certificates?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q214. How do you validate a Rancher installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q215. How do you troubleshoot Rancher Pods in CrashLoopBackOff?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q216. How do you troubleshoot Rancher UI or API unavailability?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q217. How do you troubleshoot websocket or proxy failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **install and operate an HA Rancher management plane**.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
curl -vk https://rancher.example.com/ping
kubectl -n cattle-system get deploy,pods,svc,secret
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q218. How do you troubleshoot certificate-chain errors?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **install and operate an HA Rancher management plane**.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system get deploy,pods,svc,secret
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q219. How do you recover a failed Rancher management node?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q220. How do you create a production Rancher installation runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include single-node management, certificate-chain errors, websocket or proxy failure, bad no_proxy, and unsupported chart values. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **DNS, load balancer, and TLS → supported Kubernetes management cluster → Rancher Helm release → Rancher server Pods and API → agent connectivity** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 12. Rancher Cluster Provisioning, RKE2, K3s, and Imported Clusters

## Q221. How does Rancher provision downstream clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q222. How do RKE2 and K3s clusters differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q223. Why is RKE1 no longer appropriate for new Rancher environments?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q224. How do machine-provisioned and custom-node clusters differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q225. How do imported clusters differ from provisioned clusters?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q226. How do cluster agents and node agents work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q227. How do provisioning clusters and management clusters differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q228. How do machine pools work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q229. How do node roles affect RKE2 clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q230. How do cloud credentials and node drivers work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q231. How do you register custom nodes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q232. How do you scale a downstream cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q233. How do you rotate certificates in downstream clusters?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q234. How do you upgrade Kubernetes through Rancher?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q235. How do you troubleshoot a cluster stuck Provisioning?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q236. How do you troubleshoot a node stuck Registering?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q237. How do you troubleshoot cattle-cluster-agent disconnection?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q238. How do you troubleshoot cattle-node-agent failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provision and manage RKE2, K3s, custom-node, and imported clusters**.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q239. How do you recover an imported cluster after Rancher outage?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pods -A | grep -E 'cattle-cluster-agent|cattle-node-agent'
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q240. How do you create a Rancher provisioning standard?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
```

**Key risks:** Typical risks include cloud credential failure, node-driver errors, registration-token issues, agent disconnect, certificate problems, and unsupported RKE1. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **Rancher provisioning object → machine or node registration → system-agent plan → Kubernetes bootstrap → cattle agents → managed cluster status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 13. Rancher Authentication, Authorization, Projects, and Multi-Tenancy

## Q241. Explain Rancher authentication architecture.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get globalroles.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q242. How do local users differ from external identity-provider users?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Rancher role templates connect global, cluster, and project access to Kubernetes RBAC.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get globalroles.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q243. How do Active Directory, LDAP, SAML, OIDC, and GitHub authentication differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get globalroles.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q244. How do you configure an external authentication provider?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q245. How do Rancher global roles work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q246. How do cluster roles and project roles differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q247. How do role templates map to Kubernetes RBAC?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q248. How do projects group namespaces?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get globalroles.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q249. How do project quotas work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q250. How do project network isolation settings work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q251. How do custom roles work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q252. How do you design least-privilege Rancher access?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q253. How do you troubleshoot a user who cannot see a cluster?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q254. How do you troubleshoot a user with unexpected access?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q255. How do you troubleshoot external authentication failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalroles.management.cattle.io
kubectl get globalrolebindings.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q256. How do you recover after identity-provider lockout?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher role templates connect global, cluster, and project access to Kubernetes RBAC.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get globalroles.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q257. How do you design break-glass access?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalroles.management.cattle.io
kubectl get globalrolebindings.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q258. How do you perform periodic access reviews?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **provide federated authentication and least-privilege Rancher tenancy**.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q259. How do you audit Rancher authorization changes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get roletemplates.management.cattle.io
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q260. How do you design enterprise Rancher tenancy?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get globalrolebindings.management.cattle.io
kubectl get roletemplates.management.cattle.io
```

**Key risks:** Typical risks include IdP lockout, role-template overreach, stale principals, unexpected inherited access, and missing break-glass credentials. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **local or external IdP authentication → Rancher principal → global, cluster, and project role templates → Kubernetes RBAC authorization** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 14. Rancher Fleet, GitOps, Continuous Delivery, and Bundles

## Q261. Explain Fleet architecture in Rancher.

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
kubectl get gitrepos.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q262. How do GitRepo, Bundle, BundleDeployment, and ClusterGroup resources relate?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundles.fleet.cattle.io -A
kubectl get bundledeployments.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q263. How does Fleet pull content from Git?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
kubectl get gitrepos.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q264. How do target customizations work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
kubectl get gitrepos.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q265. How do cluster labels drive Fleet targeting?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
kubectl get gitrepos.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q266. How do Helm and raw manifests work in Fleet?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
kubectl get gitrepos.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q267. How do overlays and values differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundles.fleet.cattle.io -A
kubectl get bundledeployments.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q268. How do you register a private Git repository?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundles.fleet.cattle.io -A
kubectl get bundledeployments.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q269. How do you protect Fleet repository credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundledeployments.fleet.cattle.io -A
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q270. How do you create a GitRepo for multi-cluster deployment?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get gitrepos.fleet.cattle.io -A
kubectl get bundles.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q271. How do you pause Fleet reconciliation?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get gitrepos.fleet.cattle.io -A
kubectl get bundles.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q272. How do you force a Fleet rescan?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundledeployments.fleet.cattle.io -A
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q273. How do you troubleshoot a GitRepo authentication failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundledeployments.fleet.cattle.io -A
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q274. How do you troubleshoot a Bundle stuck NotReady?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundledeployments.fleet.cattle.io -A
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q275. How do you troubleshoot a BundleDeployment failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get gitrepos.fleet.cattle.io -A
kubectl get bundles.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q276. How do you handle drift and manual changes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get gitrepos.fleet.cattle.io -A
kubectl get bundles.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q277. How do you design promotion across environments?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundledeployments.fleet.cattle.io -A
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=30m
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q278. How do you prevent a bad Git commit from impacting every cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **deliver multi-cluster GitOps through Rancher Fleet**.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundles.fleet.cattle.io -A
kubectl get bundledeployments.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q279. How do you monitor Fleet at scale?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get gitrepos.fleet.cattle.io -A
kubectl get bundles.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q280. How do you create enterprise Fleet governance?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get bundles.fleet.cattle.io -A
kubectl get bundledeployments.fleet.cattle.io -A
```

**Key risks:** Typical risks include bad commit fan-out, repository auth failure, Bundle not ready, incorrect cluster labels, and manual drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **GitRepo → repository polling and rendering → Bundle → target selection → BundleDeployment → downstream apply and status** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 15. Rancher–Harvester Integration and Virtualization Management

## Q281. How does Rancher import and manage a Harvester cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q282. How does the Harvester UI extension work in Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q283. How do Rancher and Harvester version compatibility affect features?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get apps.catalog.cattle.io -A | grep -i harvester
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q284. How do you create Harvester cloud credentials in Rancher?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q285. How does the Harvester node driver provision guest-cluster VMs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
kubectl get secrets -A | grep -i harvester
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q286. How do VM machine pools work for Rancher-provisioned clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q287. How do VM templates affect guest-cluster provisioning?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q288. How do networks and SSH users affect machine provisioning?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q289. How do cloud-init and guest agents affect node registration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q290. How does Rancher track Harvester VM lifecycle?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q291. How do you troubleshoot Harvester not appearing in Rancher?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q292. How do you troubleshoot invalid Harvester cloud credentials?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get apps.catalog.cattle.io -A | grep -i harvester
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q293. How do you troubleshoot Harvester node-driver unavailability?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q294. How do you troubleshoot VM machine pools stuck provisioning?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q295. How do you troubleshoot Rancher–Harvester certificate mismatch?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A | grep -i harvester
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q296. How do you troubleshoot version incompatibility?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q297. How do you remove and re-import Harvester safely?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q298. How do you isolate Harvester administration from tenant users?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get apps.catalog.cattle.io -A | grep -i harvester
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q299. How do you design Rancher–Harvester integration across sites?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get apps.catalog.cattle.io -A | grep -i harvester
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q300. How do you create an integration troubleshooting runbook?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **integrate Rancher virtualization management and Harvester VM provisioning**.

**Controller flow:** `Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get secrets -A | grep -i harvester
kubectl -n cattle-system logs deploy/rancher --since=30m
```

**Key risks:** Typical risks include version mismatch, CA trust failure, invalid cloud credential, unavailable node driver, and namespace permission conflict. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Harvester import and UI extension → cloud credential and node driver → VM machine pool → Harvester VM creation → guest node registration** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 16. Guest Kubernetes Clusters on Harvester, Cloud Provider, CSI, and Load Balancers

## Q301. How does Rancher provision RKE2 or K3s guest clusters on Harvester?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q302. How does the Harvester cloud provider integrate with guest clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. The Harvester cloud provider and CSI driver integrate guest Kubernetes clusters with VM infrastructure, load balancing, and persistent Harvester-backed volumes.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q303. How does the Harvester CSI driver provide persistent storage?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,storageclass -A
kubectl get svc -A --field-selector spec.type=LoadBalancer
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q304. How do guest-cluster StorageClasses map to Harvester volumes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get svc -A --field-selector spec.type=LoadBalancer
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q305. How do LoadBalancer Services work on Harvester guest clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get svc -A --field-selector spec.type=LoadBalancer
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q306. How do kube-vip or Harvester load balancer integrations work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q307. How do guest-cluster node networks affect connectivity?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q308. How do cloud-init and machine-config values affect guest nodes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get svc -A --field-selector spec.type=LoadBalancer
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q309. How do guest clusters access DNS, NTP, and registries?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q310. How do you resize guest-cluster nodes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,storageclass -A
kubectl get svc -A --field-selector spec.type=LoadBalancer
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q311. How do you add worker pools to a guest cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A -o wide
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q312. How do you troubleshoot guest nodes that remain unavailable?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A -o wide
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q313. How do you troubleshoot a cloud-provider initialization failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. The Harvester cloud provider and CSI driver integrate guest Kubernetes clusters with VM infrastructure, load balancing, and persistent Harvester-backed volumes.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,storageclass -A
kubectl get svc -A --field-selector spec.type=LoadBalancer
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q314. How do you troubleshoot CSI controller or node failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A -o wide
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q315. How do you troubleshoot a PersistentVolumeClaim stuck Pending?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q316. How do you troubleshoot a LoadBalancer Service stuck Pending?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get svc -A --field-selector spec.type=LoadBalancer
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q317. How do you troubleshoot guest-cluster DHCP failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get svc -A --field-selector spec.type=LoadBalancer
kubectl get clusters.provisioning.cattle.io -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q318. How do you troubleshoot VM image or template failure?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **operate Rancher-provisioned guest Kubernetes clusters on Harvester**.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A -o wide
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q319. How do you back up guest-cluster workloads and Harvester VMs coherently?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get pvc,storageclass -A
kubectl get svc -A --field-selector spec.type=LoadBalancer
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q320. How do you design production guest Kubernetes clusters on Harvester?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get machines.cluster.x-k8s.io -A -o wide
kubectl get pvc,storageclass -A
```

**Key risks:** Typical risks include DHCP or DNS failure, CSI Pending, cloud-provider errors, template mismatch, image failure, and LoadBalancer Pending. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **machine pool → VM template and network → VM and cloud-init → RKE2 or K3s bootstrap → cloud provider, CSI, load balancer, and agents** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 17. Monitoring, Alerting, Metrics, and Capacity Management

## Q321. How does Rancher Monitoring integrate Prometheus and Grafana?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-monitoring-system get pods
kubectl get servicemonitors,podmonitors,prometheusrules -A
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q322. How do Harvester monitoring components collect platform metrics?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl -n cattle-monitoring-system get pods
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q323. How do node, VM, storage, and network metrics differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q324. How do you enable and size Rancher Monitoring?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl -n cattle-monitoring-system get pods
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q325. How do you configure persistent storage for monitoring?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q326. How do ServiceMonitors and PodMonitors work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q327. How do you monitor Longhorn storage health?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-monitoring-system get pods
kubectl get servicemonitors,podmonitors,prometheusrules -A
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q328. How do you monitor KubeVirt VM health?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. KubeVirt represents persistent VM configuration with VirtualMachine and the active guest with VirtualMachineInstance; virt-launcher runs the guest process.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl -n cattle-monitoring-system get pods
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q329. How do you monitor Rancher management components?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q330. How do you monitor cattle and Fleet agents?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q331. How do you create capacity dashboards?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q332. How do you alert on node resource pressure?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q333. How do you alert on storage degradation and replica rebuilds?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q334. How do you alert on disconnected downstream clusters?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q335. How do you prevent high-cardinality metrics?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q336. How do you troubleshoot missing monitoring targets?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q337. How do you troubleshoot Prometheus storage exhaustion?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q338. How do you tune alert noise?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-monitoring-system get pods
kubectl get servicemonitors,podmonitors,prometheusrules -A
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q339. How do you define Harvester and Rancher SLIs and SLOs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-monitoring-system get pods
kubectl get servicemonitors,podmonitors,prometheusrules -A
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q340. How do you create an enterprise capacity and monitoring model?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **monitor platform availability, capacity, storage, VMs, and agents**.

**Controller flow:** `exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get servicemonitors,podmonitors,prometheusrules -A
kubectl top nodes
```

**Key risks:** Typical risks include missing targets, excessive cardinality, full storage, noisy alerts, and unmonitored management agents. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **exporters and ServiceMonitors → Prometheus → recording and alert rules → Alertmanager and Grafana → operational response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 18. Logging, Audit, Event Collection, and Troubleshooting Evidence

## Q341. How does Harvester logging architecture work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q342. How does Rancher Logging use the Logging Operator?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterflows,clusteroutputs -A
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q343. How do Fluent Bit and Fluentd participate in log forwarding?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q344. Which Harvester logs are collected?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl -u rke2-server --since '30 min ago'
kubectl -n cattle-logging-system get pods
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q345. How do audit and Kubernetes event logs integrate with logging?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterflows,clusteroutputs -A
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q346. How do you enable the Harvester logging add-on?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterflows,clusteroutputs -A
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q347. How do you forward logs to Elasticsearch, Splunk, Graylog, or Loki?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl -u rke2-server --since '30 min ago'
kubectl -n cattle-logging-system get pods
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q348. How do you configure TLS and credentials for external logging?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-logging-system get pods
kubectl get clusterflows,clusteroutputs -A
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q349. How do you size logging resources?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl -u rke2-server --since '30 min ago'
kubectl -n cattle-logging-system get pods
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q350. How do you prevent log backpressure from affecting nodes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl -u rke2-server --since '30 min ago'
kubectl -n cattle-logging-system get pods
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q351. How do you troubleshoot missing node logs?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q352. How do you troubleshoot log-forwarding failures?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterflows,clusteroutputs -A
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q353. How do you troubleshoot certificate errors to a log server?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-logging-system get pods
kubectl get clusterflows,clusteroutputs -A
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q354. How do you collect RKE2 service logs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl -u rke2-server --since '30 min ago'
kubectl -n cattle-logging-system get pods
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q355. How do you collect Longhorn and KubeVirt logs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. KubeVirt represents persistent VM configuration with VirtualMachine and the active guest with VirtualMachineInstance; virt-launcher runs the guest process.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q356. How do you collect Rancher agent logs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterflows,clusteroutputs -A
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q357. How do you preserve evidence during an incident?

### Answer

First contain automated impact, preserve evidence, protect data and running workloads, and restore a supported management path. During an incident, protect running workloads, preserve controller and node evidence, stop harmful Fleet or API automation, rotate exposed credentials, and correct the source of truth.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-logging-system get pods
kubectl get clusterflows,clusteroutputs -A
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q358. How do you correlate events across Rancher, Harvester, and guest clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q359. How do you manage log retention and compliance?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **centralize Harvester, Rancher, node, audit, event, and workload logs**.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl logs -n cattle-logging-system -l app.kubernetes.io/name=fluent-bit --since=30m
journalctl -u rke2-server --since '30 min ago'
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q360. How do you create an enterprise logging runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-logging-system get pods
kubectl get clusterflows,clusteroutputs -A
```

**Key risks:** Typical risks include backpressure, output TLS failure, missing node logs, audit gaps, resource pressure, and no external retention. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release compatibility.

**Verification:** Validate **Pod, journal, audit, and event sources → Fluent Bit → Fluentd or routing pipeline → external log service → query and retention** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 19. Security Hardening, Certificates, RBAC, and Supply Chain

## Q361. How do you harden Harvester management access?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q362. How do you harden Rancher management access?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterrolebindings
kubectl get certificates,issuers,clusterissuers -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q363. How do you implement least-privilege Kubernetes and Rancher RBAC?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get certificates,issuers,clusterissuers -A
kubectl get networkpolicies -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q364. How do you protect kubeconfig files?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterrolebindings
kubectl get certificates,issuers,clusterissuers -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q365. How do you secure Harvester backup credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q366. How do you secure Rancher cloud credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q367. How do you rotate API tokens and service-account credentials?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q368. How do you manage private registry credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get certificates,issuers,clusterissuers -A
kubectl get networkpolicies -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q369. How do you verify Harvester and Rancher images and artifacts?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q370. How do you harden host operating-system access?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterrolebindings
kubectl get certificates,issuers,clusterissuers -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q371. How do network policies reduce platform risk?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get certificates,issuers,clusterissuers -A
kubectl get networkpolicies -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q372. How do admission policies protect downstream clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q373. How do Pod Security standards affect Rancher applications?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl auth can-i --list
kubectl get clusterrolebindings
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q374. How do certificates and private CAs affect trust?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q375. How do you manage certificate expiry monitoring?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl auth can-i --list
kubectl get clusterrolebindings
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q376. How do you isolate management, tenant, storage, and backup networks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q377. How do you respond to a compromised cluster token?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get certificates,issuers,clusterissuers -A
kubectl get networkpolicies -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q378. How do you audit privileged access?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q379. How do you conduct a Harvester and Rancher threat model?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get networkpolicies -A
kubectl auth can-i --list
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

## Q380. How do you create an enterprise security baseline?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **harden management access, certificates, credentials, networks, and supply chain**.

**Controller flow:** `trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusterrolebindings
kubectl get certificates,issuers,clusterissuers -A
```

**Key risks:** Typical risks include compromised tokens, broad cloud credentials, certificate expiry, exposed management planes, untrusted images, and weak audit. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **trusted identity and artifacts → RBAC, TLS, network, admission, and registry controls → audit and detection → response** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet connectivity,...

---

# 20. Rancher Backup, Restore, Migration, and Management-Plane DR

## Q381. How does the Rancher Backup Operator work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get crd | grep resources.cattle.io
kubectl -n cattle-resources-system get backups,restores
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q382. What Rancher resources must be backed up?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q383. How do local, S3, and compatible backup targets differ?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get crd | grep resources.cattle.io
kubectl -n cattle-resources-system get backups,restores
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q384. How do you schedule Rancher backups?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get pods
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q385. How do you protect Rancher backup credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
kubectl get crd | grep resources.cattle.io
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q386. How do you restore Rancher into the same management cluster?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get pods
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q387. How do you migrate Rancher to a new management cluster?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get crd | grep resources.cattle.io
kubectl -n cattle-resources-system get backups,restores
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q388. How do you restore encrypted Secrets and certificates?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **back up and recover Rancher management-plane resources**.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q389. How do you validate a Rancher restore?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get crd | grep resources.cattle.io
kubectl -n cattle-resources-system get backups,restores
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q390. How do downstream clusters behave while Rancher is unavailable?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q391. How do imported clusters behave during management-plane recovery?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **back up and recover Rancher management-plane resources**.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
kubectl get crd | grep resources.cattle.io
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q392. How do you troubleshoot a failed Rancher backup?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q393. How do you troubleshoot a failed Rancher restore?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q394. How do you recover after accidental Rancher namespace deletion?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
kubectl get crd | grep resources.cattle.io
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q395. How do you preserve the Rancher server URL during migration?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get pods
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q396. How do you handle private CA trust during recovery?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **back up and recover Rancher management-plane resources**.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get pods
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q397. How do you define Rancher management-plane RTO and RPO?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system logs deploy/rancher-backup --since=30m
kubectl get crd | grep resources.cattle.io
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q398. How do you test a Rancher disaster-recovery exercise?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q399. How do you separate Rancher and workload backup strategies?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

## Q400. How do you create an enterprise Rancher DR runbook?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-resources-system get backups,restores
kubectl -n cattle-resources-system get pods
```

**Key risks:** Typical risks include missing Secrets, Rancher URL or CA mismatch, incomplete restore, untested backups, and confusion with workload backup. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **Backup custom resource → Rancher Backup Operator → management resources and Secrets → external storage → same or new management-cluster restore → agent validation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster...

---

# 21. Rancher Upgrades, Kubernetes Compatibility, and Lifecycle Management

## Q401. How do you plan a Rancher upgrade?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system list
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q402. How do Rancher support matrices affect upgrade decisions?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q403. How do you read Rancher release notes and known issues?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q404. How do you back up Rancher before upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
helm -n cattle-system list
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q405. How do you upgrade a Helm-installed Rancher server?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
helm -n cattle-system list
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q406. How do you upgrade an air-gapped Rancher installation?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q407. How do you validate CRDs and Helm values before upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q408. How do you handle custom certificates during upgrade?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q409. How do you validate downstream cluster compatibility?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **upgrade Rancher and downstream Kubernetes within supported compatibility ranges**.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q410. How do you upgrade RKE2 and K3s clusters through Rancher?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q411. How do Kubernetes version-skew rules affect upgrades?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q412. How do you migrate remaining RKE1 clusters?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. RKE1 reached end of life on July 31, 2025.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q413. How do you troubleshoot Rancher Pods failing after upgrade?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl -n cattle-system rollout status deploy/rancher
kubectl get clusters.management.cattle.io
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q414. How do you troubleshoot downstream agents after upgrade?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q415. How do you troubleshoot Fleet failures after upgrade?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
helm -n cattle-system list
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q416. How do you handle deprecated features?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **upgrade Rancher and downstream Kubernetes within supported compatibility ranges**.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
helm -n cattle-system list
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q417. How do you roll back when direct downgrade is unsafe?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **upgrade Rancher and downstream Kubernetes within supported compatibility ranges**.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q418. How do you test upgrade runbooks in non-production?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system get values rancher
kubectl -n cattle-system rollout status deploy/rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q419. How do you manage Rancher and Harvester version compatibility?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get clusters.management.cattle.io
helm -n cattle-system list
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q420. How do you create a platform lifecycle calendar?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **upgrade Rancher and downstream Kubernetes within supported compatibility ranges**.

**Controller flow:** `support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm -n cattle-system list
helm -n cattle-system get values rancher
```

**Key risks:** Typical risks include unsupported Kubernetes version, deprecated feature, agent incompatibility, Fleet regression, private CA issues, and unsafe downgrade. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets,...

**Verification:** Validate **support matrix and release notes → Rancher backup → Helm upgrade → management validation → agent and Fleet validation → downstream upgrades** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 22. Automation, APIs, Terraform, GitOps, and Operational Tooling

## Q421. How do you use Rancher APIs for automation?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q422. How do you use Harvester Kubernetes APIs for automation?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q423. How do you authenticate automation safely?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q424. How do you use Terraform with Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
terraform plan
kubectl apply --server-side --dry-run=server -f resource.yaml
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q425. How do you use Terraform with Harvester resources?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q426. How do you manage clusters declaratively?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q427. How do you automate VM provisioning?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl apply --server-side --dry-run=server -f resource.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q428. How do you automate backups and restore tests?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl apply --server-side --dry-run=server -f resource.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q429. How do you automate health checks?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl apply --server-side --dry-run=server -f resource.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q430. How do you integrate Rancher and Harvester with CI/CD?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q431. How do you use Fleet for platform add-ons?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
terraform plan
kubectl apply --server-side --dry-run=server -f resource.yaml
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q432. How do you manage Helm applications through Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q433. How do you prevent API automation from creating excessive load?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q434. How do you handle pagination and retries?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl apply --server-side --dry-run=server -f resource.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q435. How do you make automation idempotent?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q436. How do you validate automation in a test cluster?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q437. How do you protect automation credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl apply --server-side --dry-run=server -f resource.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q438. How do you audit automated changes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl api-resources | grep -E 'cattle|harvester|fleet'
terraform plan
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q439. How do you reconcile emergency manual changes?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl api-resources | grep -E 'cattle|harvester|fleet'
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q440. How do you create an enterprise automation framework?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **automate Rancher and Harvester through APIs, Terraform, Fleet, Helm, and CI/CD**.

**Controller flow:** `version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
terraform plan
kubectl apply --server-side --dry-run=server -f resource.yaml
```

**Key risks:** Typical risks include overprivileged credentials, non-idempotent changes, API overload, manual drift, destructive defaults, and missing rollback. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and release...

**Verification:** Validate **version-controlled request → authenticated API or custom resource → controller reconciliation → status and workload validation → audit** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 23. Performance, Scale, Resource Limits, and Troubleshooting Methodology

## Q441. How do you troubleshoot a slow Harvester UI?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl top nodes
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q442. How do you troubleshoot a slow Rancher UI?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl top nodes
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q443. How do you identify Kubernetes API pressure?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl top nodes
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q444. How do you identify etcd latency in management clusters?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q445. How do you troubleshoot high Longhorn storage latency?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q446. How do you troubleshoot VM CPU steal or scheduling delay?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q447. How do you troubleshoot VM network throughput problems?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get --raw '/readyz?verbose'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q448. How do you troubleshoot Rancher database growth?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q449. How do you troubleshoot Fleet reconciliation at scale?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q450. How do you identify cluster-agent resource pressure?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q451. How do you plan for thousands of managed clusters?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q452. How do you plan for thousands of VMs?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl get --raw '/readyz?verbose'
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q453. How do you tune monitoring and logging at scale?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl get --raw '/readyz?verbose'
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q454. How do you manage Kubernetes API rate limits?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl get --raw '/readyz?verbose'
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q455. How do you prevent VM migration storms?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get --raw '/readyz?verbose'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q456. How do you prevent replica-rebuild storms?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top nodes
kubectl top pods -A --containers
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q457. How do you run safe performance tests?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get --raw '/readyz?verbose'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q458. How do you build a problem timeline from events and logs?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get --raw '/readyz?verbose'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q459. How do you write a platform root-cause analysis?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl top pods -A --containers
kubectl get --raw '/readyz?verbose'
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q460. How do you create a Corporate/L3 troubleshooting methodology?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **diagnose scale, API latency, storage, VM, agent, and capacity bottlenecks**.

**Controller flow:** `user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get --raw '/readyz?verbose'
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include etcd or API pressure, storage latency, migration or rebuild storms, agent saturation, excessive telemetry, and weak baselines. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **user symptom → management API and controller → node, VM, storage, network, or downstream metrics and logs → bottleneck → reversible remediation** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

# 24. Rancher Apps, Helm Charts, Catalogs, and Cluster Tools

## Q461. How does Rancher deploy applications with Helm charts?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
helm list -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q462. How do Apps and Releases differ in Rancher?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q463. How do Rancher chart repositories work?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q464. How do system charts differ from user application charts?

### Answer

Compare the options through controller ownership, failure behavior, performance, supportability, and recovery. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q465. How do you add a private Helm repository?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
helm list -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q466. How do you protect Helm repository credentials?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q467. How do you manage chart values across environments?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm history <release> -n <namespace>
kubectl get apps.catalog.cattle.io -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q468. How do you upgrade a Rancher-managed Helm application?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester follows supported upgrade paths with limited minor-version jumps because RKE2 and Kubernetes skew is constrained.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q469. How do you roll back a failed Helm release?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q470. How do you troubleshoot a Helm application stuck Installing?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm list -A
helm history <release> -n <namespace>
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q471. How do you troubleshoot a Helm application stuck Uninstalling?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q472. How do CRDs affect Helm application lifecycle?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
helm list -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q473. How do finalizers block application deletion?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get apps.catalog.cattle.io -A
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q474. How do you manage monitoring, logging, backup, and policy applications?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester logging uses the Logging Operator with Fluent Bit and Fluentd and expects an external service for persistent log retention.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm list -A
helm history <release> -n <namespace>
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q475. How do you validate chart and Kubernetes compatibility?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm history <release> -n <namespace>
kubectl get apps.catalog.cattle.io -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q476. How do you manage charts in air-gapped environments?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm list -A
helm history <release> -n <namespace>
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q477. How do you prevent application teams from installing unsafe charts?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **manage Rancher Helm applications, catalogs, CRDs, and cluster tools safely**.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm list -A
helm history <release> -n <namespace>
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q478. How do you audit Helm release changes through Rancher?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm history <release> -n <namespace>
kubectl get apps.catalog.cattle.io -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q479. How do you reconcile manual Helm changes with Rancher management?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
helm list -A
helm history <release> -n <namespace>
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

## Q480. How do you create an enterprise Rancher application-catalog standard?

### Answer

Treat this as an HCI and multi-cluster platform decision involving failure domains, capacity, security, and lifecycle. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
helm list -A
```

**Key risks:** Typical risks include untrusted charts, CRD lifecycle errors, finalizer blocks, incompatible Kubernetes versions, secret exposure, and manual Helm drift. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup targets, and...

**Verification:** Validate **chart repository and version → Helm values → Rancher application or Helm release → CRDs and workloads → status, upgrade, or rollback** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health, cattle/Fleet...

---

# 25. Corporate L3 Harvester and Rancher Incident Scenarios

## Q481. A Harvester node fails with multiple running VMs. How do you respond?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl cluster-info dump --output-directory=cluster-dump
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q482. Longhorn reports several degraded volumes during peak traffic. How do you stabilize the platform?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Longhorn provides synchronous replicated block storage using engines and replicas distributed across approved nodes and disks.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q483. A VM network change disconnects an entire tenant. How do you recover?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl --since '1 hour ago'
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q484. A Harvester upgrade is stuck and nodes are partially upgraded. How do you proceed?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl cluster-info dump --output-directory=cluster-dump
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q485. Rancher is unavailable but downstream clusters are still running. How do you lead recovery?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl --since '1 hour ago'
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q486. All cattle-cluster-agents disconnect after a certificate change. How do you investigate?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q487. An identity-provider change locks out all Rancher administrators. How do you recover?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Rancher role templates connect global, cluster, and project access to Kubernetes RBAC.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q488. A Fleet GitRepo deploys a broken configuration to many clusters. How do you contain it?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Fleet turns Git content into Bundles and BundleDeployments targeted to clusters.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q489. A Rancher–Harvester integration upgrade breaks VM provisioning. How do you recover?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q490. Guest-cluster nodes remain unavailable after VM creation. How do you troubleshoot?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q491. Harvester CSI volumes remain Pending in a production guest cluster. How do you respond?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl --since '1 hour ago'
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q492. LoadBalancer Services remain Pending across several guest clusters. How do you investigate?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
journalctl --since '1 hour ago'
kubectl get nodes,pods -A -o wide
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q493. A backup restore completes but the application is inconsistent. How do you recover?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Backups are trustworthy only after successful restore and application validation.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl cluster-info dump --output-directory=cluster-dump
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q494. Monitoring storage fills and alerts stop. How do you restore observability?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get nodes,pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q495. Logging stops forwarding audit data during an incident. How do you preserve evidence?

### Answer

First contain automated impact, preserve evidence, protect data and running workloads, and restore a supported management path. During an incident, protect running workloads, preserve controller and node evidence, stop harmful Fleet or API automation, rotate exposed credentials, and correct the source of truth.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q496. A compromised Rancher API token is actively being used. How do you contain and investigate?

### Answer

Start by isolating the first failing controller, agent, node, VM, storage, network, or external dependency instead of repeatedly restarting Pods. Rancher is a Kubernetes management platform running on a management cluster and using agents and custom resources to provision or manage downstream clusters.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q497. A bad automation job deletes critical Rancher or Harvester resources. How do you recover?

### Answer

Perform this through a supported, staged, reversible procedure with prechecks and post-validation. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q498. How do you lead a Harvester and Rancher production incident bridge?

### Answer

First contain automated impact, preserve evidence, protect data and running workloads, and restore a supported management path. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl cluster-info dump --output-directory=cluster-dump
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q499. What evidence belongs in a Harvester and Rancher root-cause analysis?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. Harvester is a Kubernetes-based HCI platform combining RKE2, KubeVirt, Longhorn, networking, and controller-managed lifecycle operations.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl cluster-info dump --output-directory=cluster-dump
journalctl --since '1 hour ago'
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---

## Q500. What should a high-quality Corporate/L3 platform runbook contain?

### Answer

Explain this from Kubernetes controller mechanics and enterprise platform operations. This topic belongs to the Corporate/L3 responsibility to **lead containment, recovery, evidence preservation, communication, and prevention**.

**Controller flow:** `declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA`

**Corporate/L3 approach**

1. Confirm the exact Rancher, Harvester, Kubernetes, RKE2/K3s, Longhorn, KubeVirt, CSI, cloud-provider, and agent versions.
2. Identify the owning custom resource, controller, node-side component, and external dependency.
3. Preserve conditions, events, logs, metrics, Git or Helm changes, credentials metadata, and node journals.
4. Test the smallest reversible fix on one node, VM, cluster, or tenant.
5. Validate management access, data integrity, storage, networking, agents, and the actual application path.
6. Correct Git, Fleet, Terraform, Helm values, templates, monitoring, and runbooks before broad reconciliation.

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl cluster-info dump --output-directory=cluster-dump
```

**Key risks:** Typical risks include uncoordinated changes, evidence destruction, credential mishandling, source-of-truth mismatch, repeated automation damage, and ownerless actions. Also verify DNS, NTP, TLS and private CA trust, VIPs, load balancers, proxies, registry access, capacity, disk latency, replica health, MTU/VLAN settings, tokens, RBAC, PDBs, backup...

**Verification:** Validate **declare and scope → stop harmful automation → preserve Rancher, Harvester, Kubernetes, VM, storage, network, and Git evidence → recover → validate → RCA** end to end. Confirm management access, controller conditions, node readiness, VM state, storage robustness, network reachability, guest-cluster health,...

---
