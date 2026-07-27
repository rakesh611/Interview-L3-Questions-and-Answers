# OpenShift Advanced Cluster Management (EX432) - 500 Corporate L3 Interview Questions and Detailed Answers

> Git-compatible edition with exactly 500 questions across 25 domains.  
> Exam alignment: OpenShift Container Platform 4.18 and RHACM 2.13.  
> UTF-8, Unix line endings, balanced YAML and Bash fences, and a simple ASCII filename.  
> Independent study material, not an official exam dump.

## Corporate/L3 Method

Identify the hub resource, controller, managed-cluster agent or add-on, cluster set, placement, user, and target workload. Trace OLM, registration, work delivery, governance, observability, GitOps, or virtualization. Preserve YAML, conditions, Events, logs, PlacementDecisions, policies, Application history, and recent changes. Test on one managed cluster or canary cluster set and ensure the configuration persists.

## Table of Contents

- [01. RHACM Architecture, Hub, Managed Clusters, and Core Components](#01-rhacm-architecture-hub-managed-clusters-and-core-components)
- [02. Operator Lifecycle Manager Installation and MultiClusterHub Deployment](#02-operator-lifecycle-manager-installation-and-multiclusterhub-deployment)
- [03. Managed-Cluster Import, Klusterlet Agents, and Registration](#03-managed-cluster-import-klusterlet-agents-and-registration)
- [04. Cluster Creation, Infrastructure Environments, and Provisioning](#04-cluster-creation-infrastructure-environments-and-provisioning)
- [05. Cluster Upgrade, Scaling, Detach, Destroy, and Lifecycle Operations](#05-cluster-upgrade-scaling-detach-destroy-and-lifecycle-operations)
- [06. ManagedClusterSet, Cluster Claims, Labels, and Fleet Organization](#06-managedclusterset-cluster-claims-labels-and-fleet-organization)
- [07. Placement, PlacementDecision, Cluster Selectors, and Scheduling](#07-placement-placementdecision-cluster-selectors-and-scheduling)
- [08. Multicluster RBAC, Roles, Cluster Sets, and Access Control](#08-multicluster-rbac-roles-cluster-sets-and-access-control)
- [09. Search Service, Queries, Console Search, and Fleet Inventory](#09-search-service-queries-console-search-and-fleet-inventory)
- [10. Governance Architecture, Policy Framework, and Propagation](#10-governance-architecture-policy-framework-and-propagation)
- [11. ConfigurationPolicy, Object Templates, Compliance, and Remediation](#11-configurationpolicy-object-templates-compliance-and-remediation)
- [12. PolicyGenerator, Kustomize, PolicySets, and Policy-as-Code](#12-policygenerator-kustomize-policysets-and-policy-as-code)
- [13. Compliance Operator Across Multiple Clusters](#13-compliance-operator-across-multiple-clusters)
- [14. Governance Troubleshooting, Events, Templates, and Add-ons](#14-governance-troubleshooting-events-templates-and-add-ons)
- [15. Observability Architecture, Installation, and Object Storage](#15-observability-architecture-installation-and-object-storage)
- [16. Observability Customization, Metrics, Alerts, Grafana, and Retention](#16-observability-customization-metrics-alerts-grafana-and-retention)
- [17. Git, Kustomize, Repository Design, and Multicluster Configuration](#17-git-kustomize-repository-design-and-multicluster-configuration)
- [18. OpenShift GitOps Operator, Argo CD, and Managed-Cluster Import](#18-openshift-gitops-operator-argo-cd-and-managed-cluster-import)
- [19. Multicluster Application Lifecycle, Placement, and GitOps Delivery](#19-multicluster-application-lifecycle-placement-and-gitops-delivery)
- [20. OpenShift Virtualization Operator Deployment with RHACM and GitOps](#20-openshift-virtualization-operator-deployment-with-rhacm-and-gitops)
- [21. Multicluster Virtual Machines, Search, Actions, and Monitoring](#21-multicluster-virtual-machines-search-actions-and-monitoring)
- [22. Backup, Restore, Hub Recovery, and Business Continuity](#22-backup-restore-hub-recovery-and-business-continuity)
- [23. Networking, Add-ons, Submariner, and Managed-Cluster Connectivity](#23-networking-add-ons-submariner-and-managed-cluster-connectivity)
- [24. Upgrades, Capacity, Security, Troubleshooting, and Platform Operations](#24-upgrades-capacity-security-troubleshooting-and-platform-operations)
- [25. Corporate L3 EX432 Incident and Design Scenarios](#25-corporate-l3-ex432-incident-and-design-scenarios)

---

# 01. RHACM Architecture, Hub, Managed Clusters, and Core Components

## Q001. Explain Red Hat Advanced Cluster Management architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM provides cluster lifecycle, governance, search, observability, application GitOps, access control, and integrations from an OpenShift hub.

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q002. What responsibilities belong to the hub cluster?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q003. What responsibilities belong to a managed cluster?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q004. How does the multicluster engine operator relate to RHACM?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. The multicluster engine operator provides cluster lifecycle capabilities and is installed as part of RHACM.

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q005. What is the purpose of the MultiClusterHub custom resource?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q006. How do klusterlet components work on managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. The klusterlet registration and work agents run on a managed cluster, register it with the hub, and execute ManifestWork.

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q007. How does the work manager deliver resources to managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q008. How do registration and work APIs interact?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q009. How do ManifestWork resources participate in multicluster management?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q010. How do addon-framework components extend managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q011. What are cluster-management add-ons?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q012. How does the RHACM console integrate with the OpenShift console?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q013. How do search, governance, observability, and GitOps components differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q014. How does RHACM store desired and observed cluster state?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q015. How do hub and managed-cluster failure domains differ?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**....

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q016. How does RHACM support non-OpenShift Kubernetes clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q017. How do pull-based and hub-driven operations differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q018. How do you assess overall RHACM platform health?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q019. How do you identify the controller responsible for a failed operation?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters
oc get multiclusterhub,multiclusterengine -A
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q020. What are Corporate/L3 RHACM administrator responsibilities?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACM hub and managed-cluster components**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `hub resource or user action → RHACM or multicluster-engine controller → registration, work, add-on, search, governance, observability, or GitOps flow → managed cluster status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub,multiclusterengine -A
oc get managedclusters
```

**Risks:** unclear controller ownership, hub single-point assumptions, stale cluster state, and confusing desired with observed state. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 02. Operator Lifecycle Manager Installation and MultiClusterHub Deployment

## Q021. How do you install RHACM from OperatorHub?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q022. Which namespace should host the RHACM operator?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q023. How do Subscription, OperatorGroup, InstallPlan, and CSV resources interact?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q024. How do automatic and manual install-plan approval differ?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q025. How do you select an RHACM update channel?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q026. How do you create the MultiClusterHub resource?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q027. How do you monitor MultiClusterHub installation progress?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q028. How do component availability settings affect installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q029. How do node selectors and tolerations affect RHACM components?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q030. How do proxy settings affect RHACM installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q031. How do disconnected registries affect installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q032. How do you mirror RHACM operator catalogs and images?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q033. How do custom certificate authorities affect installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q034. How do you validate CRD installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q035. How do you validate operator and operand health?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q036. How do you troubleshoot a Subscription stuck UpgradePending?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q037. How do you troubleshoot a CSV in Failed state?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q038. How do you troubleshoot MultiClusterHub stuck Progressing?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q039. How do you uninstall RHACM safely?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get subscription,installplan,csv -n open-cluster-management
oc get multiclusterhub -n open-cluster-management -o yaml
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q040. How do you create an enterprise installation runbook?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy RHACM through OLM and MultiClusterHub**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `CatalogSource and Subscription → InstallPlan and CSV → operator Deployment and CRDs → MultiClusterHub → operands and console plug-in`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: operator.open-cluster-management.io/v1
kind: MultiClusterHub
metadata:
  name: multiclusterhub
  namespace: open-cluster-management
spec: {}
```

```bash
oc get multiclusterhub -n open-cluster-management -o yaml
oc get subscription,installplan,csv -n open-cluster-management
```

**Risks:** wrong channel, failed CSV, missing mirrored images, proxy or CA issues, and resource starvation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 03. Managed-Cluster Import, Klusterlet Agents, and Registration

## Q041. How do you import an existing OpenShift cluster into RHACM?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q042. How do you import a generic CNCF-conformant Kubernetes cluster?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q043. What resources are created on the hub during cluster import?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q044. What resources are installed on a managed cluster during import?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q045. How do bootstrap and import Secrets work?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q046. How does the ManagedCluster resource represent an imported cluster?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. ManagedCluster is the hub representation of a registered cluster and records acceptance, claims, labels, and availability conditions.

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q047. How does the Klusterlet resource work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. The klusterlet registration and work agents run on a managed cluster, register it with the hub, and execute ManifestWork.

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q048. How do cluster claims provide managed-cluster metadata?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q049. How do managed-service-account add-ons support access?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q050. How do proxies affect agent connectivity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q051. How do private API endpoints affect cluster import?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q052. How do custom CA certificates affect import?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q053. How do you rotate import credentials?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q054. How do you validate klusterlet registration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. The klusterlet registration and work agents run on a managed cluster, register it with the hub, and execute ManifestWork.

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q055. How do you validate work-agent connectivity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q056. How do you troubleshoot a ManagedCluster stuck Unknown?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. ManagedCluster is the hub representation of a registered cluster and records acceptance, claims, labels, and...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q057. How do you troubleshoot klusterlet registration failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. The klusterlet registration and work agents run on a managed cluster, register it with the hub, and execute...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q058. How do you troubleshoot work-agent disconnection?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**....

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q059. How do you re-import a cluster safely?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedcluster <cluster> -o yaml
oc get klusterlet -A
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q060. How do you create a managed-cluster onboarding standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **register existing clusters and maintain agents**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `ManagedCluster and import Secret → klusterlet registration → accepted registration → work agent and add-ons → Available status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get klusterlet -A
oc get managedcluster <cluster> -o yaml
```

**Risks:** expired bootstrap data, CA or proxy failure, duplicate cluster names, and disconnected agents. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 04. Cluster Creation, Infrastructure Environments, and Provisioning

## Q061. How does RHACM create OpenShift clusters?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q062. How do Hive and assisted-service components participate in provisioning?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q063. What is a ClusterDeployment resource?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q064. What is a ClusterImageSet resource?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q065. How do install-config Secrets participate in provisioning?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q066. How do cloud-credential Secrets participate in provisioning?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q067. How do BareMetalHost resources support bare-metal provisioning?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q068. How do InfraEnv resources support assisted installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q069. How do Agent resources represent discovered hosts?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q070. How do NMStateConfig resources configure host networking?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q071. How do you create a cluster on AWS through RHACM?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q072. How do you create a cluster on bare metal through RHACM?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q073. How do you create a disconnected cluster?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q074. How do you select an OpenShift release image?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q075. How do you provide pull Secrets and SSH keys?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q076. How do you troubleshoot cluster creation stuck Installing?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q077. How do you troubleshoot host validation failures?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q078. How do you troubleshoot cloud credential failures?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q079. How do you preserve evidence from a failed installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get infraenvs,agents -A
oc get clusterdeployments.hive.openshift.io -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q080. How do you create an enterprise cluster-provisioning workflow?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **create OpenShift clusters through RHACM lifecycle APIs**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `cluster definition and credentials → Hive or assisted-service resources → infrastructure and installation → ManagedCluster registration`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterdeployments.hive.openshift.io -A
oc get infraenvs,agents -A
```

**Risks:** invalid credentials, release-image mismatch, host validation failure, and incomplete cleanup. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 05. Cluster Upgrade, Scaling, Detach, Destroy, and Lifecycle Operations

## Q081. How do you upgrade a managed OpenShift cluster through RHACM?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q082. How does ClusterVersion status affect RHACM upgrade visibility?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q083. How do you select an approved OpenShift update channel?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q084. How do you stage upgrades across a fleet?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q085. How do maintenance windows affect cluster upgrades?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q086. How do you scale machine pools through RHACM?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q087. How do MachinePool and MachineSet resources differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q088. How do you scale control-plane and worker capacity?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q089. How do you validate cluster health before upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q090. How do you validate cluster health after upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q091. How do you pause or defer an upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q092. How do you handle clusters blocked by unavailable Operators?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**....

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q093. How do you detach a managed cluster?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q094. How does detach differ from destroy?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q095. How do you destroy an RHACM-provisioned cluster?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q096. How do finalizers affect cluster removal?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q097. How do you troubleshoot a cluster stuck Detaching?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**....

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q098. How do you troubleshoot a cluster stuck Destroying?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**....

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get machinepools.hive.openshift.io -A
oc get clusterversions -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q099. How do you recover from a partially completed upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q100. How do you create a cluster-lifecycle operations standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **upgrade, scale, detach, and destroy clusters safely**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `fleet plan → cluster health and lifecycle resource update → OpenShift or infrastructure controller → validation or removal`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clusterversions -A
oc get machinepools.hive.openshift.io -A
```

**Risks:** unsupported upgrade, unavailable Operators, finalizer blockage, capacity shortage, and accidental destruction. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 06. ManagedClusterSet, Cluster Claims, Labels, and Fleet Organization

## Q101. What is a ManagedClusterSet?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ManagedClusterSet groups clusters into an administrative and placement boundary and requires namespace bindings for consumers. ManagedCluster is the hub representation of a registered cluster and records...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q102. How do you bind clusters to a ManagedClusterSet?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ManagedClusterSet groups clusters into an administrative and placement boundary and requires namespace bindings for consumers. ManagedCluster is the hub representation of a registered cluster and records...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q103. What is a ManagedClusterSetBinding?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ManagedClusterSet groups clusters into an administrative and placement boundary and requires namespace bindings for consumers. ManagedCluster is the hub representation of a registered cluster and records...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q104. How do cluster-set boundaries support tenancy?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q105. How do cluster labels support organization?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q106. How do cluster claims differ from labels?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q107. How do you label clusters by environment, region, and business unit?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q108. How do you move a cluster between cluster sets safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q109. How do cluster-set membership changes affect placements?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q110. How do cluster-set bindings affect namespace access?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q111. How do you create a cluster inventory taxonomy?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q112. How do you prevent label drift?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q113. How do you audit cluster-label changes?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q114. How do you identify unlabeled or misclassified clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q115. How do you organize production and non-production clusters?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q116. How do you organize virtualization clusters?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q117. How do you troubleshoot a cluster missing from a cluster set?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**....

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q118. How do you troubleshoot a namespace without cluster-set access?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**....

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q119. How do you decommission an empty cluster set?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclusters --show-labels
oc get managedclustersets,managedclustersetbindings -A
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q120. How do you create an enterprise fleet-organization standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **organize clusters into controlled fleet boundaries**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `ManagedCluster labels and claims → ManagedClusterSet membership → ManagedClusterSetBinding → placements and RBAC`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta2
kind: ManagedClusterSet
metadata:
  name: production
spec:
  clusterSelector:
    selectorType: ExclusiveClusterSetLabel
```

```bash
oc get managedclustersets,managedclustersetbindings -A
oc get managedclusters --show-labels
```

**Risks:** misclassification, cross-tenant access, label drift, and unsafe movement between sets. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 07. Placement, PlacementDecision, Cluster Selectors, and Scheduling

## Q121. Explain the Placement API.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q122. How do Placement and PlacementDecision resources interact?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q123. How do predicates select clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q124. How do label selectors affect placement?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q125. How do claim selectors affect placement?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q126. How do cluster sets constrain placement?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q127. How does numberOfClusters affect scheduling?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q128. How do prioritizers affect placement order?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q129. How do resource-score prioritizers work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q130. How does Steady prioritization reduce unnecessary movement?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q131. How do tolerations affect unavailable clusters?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q132. How do decision groups work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q133. How do you use Placement with governance policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q134. How do you use Placement with GitOps applications?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q135. How do you preview placement decisions?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q136. How do you troubleshoot a Placement with no decisions?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q137. How do you troubleshoot unexpected cluster selection?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc describe placement <name> -n <namespace>
oc get placements,placementdecisions -A
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q138. How do cluster-label changes affect decisions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **schedule policies and applications onto eligible clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q139. How do you prevent unsafe fleet-wide placement?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q140. How do you create enterprise placement standards?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Placement selectors and cluster sets → scheduling and prioritization → PlacementDecision → policy or GitOps consumer`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: Placement
metadata:
  name: production-eu
  namespace: fleet-config
spec:
  clusterSets:
    - production
  predicates:
    - requiredClusterSelector:
        labelSelector:
          matchLabels:
            region: eu
```

```bash
oc get placements,placementdecisions -A
oc describe placement <name> -n <namespace>
```

**Risks:** empty decisions, broad selectors, label races, and fleet-wide targeting. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 08. Multicluster RBAC, Roles, Cluster Sets, and Access Control

## Q141. Explain RHACM multicluster RBAC architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q142. How does OpenShift RBAC apply to RHACM resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q143. How do ClusterRole and Role differ in RHACM access control?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q144. How do ClusterRoleBinding and RoleBinding differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q145. How do managed-cluster roles differ from hub roles?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q146. How do cluster-set administrator and viewer roles work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q147. How do you grant access to a ManagedClusterSet?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ManagedClusterSet groups clusters into an administrative and placement boundary and requires namespace bindings for consumers. ManagedCluster is the hub representation of a registered cluster and records...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q148. How do you restrict users to selected clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q149. How do you restrict users to selected namespaces?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q150. How do you delegate policy administration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q151. How do you delegate application administration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q152. How do you protect credential Secrets?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q153. How do you use `oc auth can-i` for RHACM access checks?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q154. How do group mappings affect multicluster permissions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q155. How do you design break-glass access?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q156. How do you audit fleet RBAC changes?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q157. How do you troubleshoot a user who cannot view clusters?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q158. How do you troubleshoot unexpected cluster access?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc auth can-i --list
oc get rolebindings,clusterrolebindings -A | grep -i cluster
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q159. How do you review access periodically?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q160. How do you create an enterprise RHACM RBAC model?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **enforce least-privilege multicluster access**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `authenticated user or group → OpenShift RBAC and cluster-set permissions → RHACM API or console operation → allowed resource view or action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get rolebindings,clusterrolebindings -A | grep -i cluster
oc auth can-i --list
```

**Risks:** broad cluster-set bindings, exposed Secrets, inherited privilege, and missing break-glass access. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 09. Search Service, Queries, Console Search, and Fleet Inventory

## Q161. Explain RHACM Search architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q162. How does Search collect resource data from managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q163. Which resources are indexed by Search?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q164. How do search collectors communicate with the hub?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q165. How do you use the console search page?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q166. How do you query by kind, namespace, cluster, label, and status?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **query fleet inventory and operational state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q167. How do you combine search filters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q168. How do you search for noncompliant resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q169. How do you search for unhealthy clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q170. How do you search for virtual machines across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q171. How do you search from the CLI or API?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q172. How do RBAC permissions affect search results?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q173. How do you reduce Search data volume?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q174. How do you monitor Search component health?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q175. How do you troubleshoot missing cluster data?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **query fleet inventory and operational state**. Identify...

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q176. How do you troubleshoot stale search results?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console...

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q177. How do you troubleshoot Search API errors?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console...

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q178. How do you identify cluster-wide resource drift with Search?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters -o wide
oc get pods -n open-cluster-management | grep search
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q179. How do you use Search during an incident?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. During an RHACM incident, preserve the exact Placement, Policy, ApplicationSet, ManagedCluster, add-on, operator, event, log, and...

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q180. How do you create enterprise search and inventory workflows?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `managed-cluster collectors → search index and API → RBAC-filtered query → console or automation result`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get pods -n open-cluster-management | grep search
oc get managedclusters -o wide
```

**Risks:** stale index, collector disconnect, excessive data, and misleading incident conclusions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 10. Governance Architecture, Policy Framework, and Propagation

## Q181. Explain RHACM governance architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q182. What is a Policy resource?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q183. What is a PolicyTemplate?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q184. How do root and replicated policies differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q185. How does the governance-policy-framework add-on work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q186. How does the policy propagator work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q187. How does the configuration-policy controller work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q188. How does Placement select policy target clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q189. How does PlacementBinding differ from Placement-based binding?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q190. How do remediationAction Inform and Enforce differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q191. How do policy severity and categories support governance?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q192. How do policy dependencies work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q193. How do disabled policies behave?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q194. How do policy-set resources group policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q195. How do PolicySet and Placement interact?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q196. How do you stage governance policies safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q197. How do you identify the source of a replicated policy?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies,policysets,placements,placementdecisions -A
oc get clustermanagementaddons | grep governance
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q198. How do you troubleshoot a policy not propagating?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**....

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q199. How do you troubleshoot a managed cluster missing governance add-ons?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**....

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q200. How do you create an enterprise governance architecture?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **propagate and evaluate fleet governance policies**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `root Policy and templates → PlacementDecision → replicated policy → managed-cluster governance controllers → compliance status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get clustermanagementaddons | grep governance
oc get policies,policysets,placements,placementdecisions -A
```

**Risks:** wrong targets, missing add-ons, Inform or Enforce confusion, and replicated-policy drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 11. ConfigurationPolicy, Object Templates, Compliance, and Remediation

## Q201. Explain the ConfigurationPolicy resource.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ConfigurationPolicy compares object templates against managed-cluster resources and can report or remediate drift.

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q202. How do object-templates define required resources?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q203. How do musthave, mustonlyhave, and mustnothave differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q204. How do metadataComplianceType and spec compliance differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q205. How do namespaceSelector settings work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q206. How do objectSelector settings work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q207. How do evaluation intervals affect policy processing?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q208. How do pruneObjectBehavior settings work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q209. How do recreateOption settings work?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q210. How do recordDiff settings work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q211. How do hub templates access hub resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q212. How do managed-cluster templates access local resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q213. How do you enforce namespace labels?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q214. How do you enforce Operator subscriptions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q215. How do you enforce MachineConfig resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q216. How do you enforce OpenShift Virtualization configuration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q217. How do you troubleshoot object-template syntax errors?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q218. How do you troubleshoot Enforce not remediating drift?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q219. How do you avoid destructive mustonlyhave policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce Kubernetes resource state with ConfigurationPolicy**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc get configurationpolicies.policy.open-cluster-management.io -A
oc describe policy <name> -n <namespace>
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q220. How do you create enterprise ConfigurationPolicy standards?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. ConfigurationPolicy compares object templates against managed-cluster resources and can report or remediate drift.

**Flow:** `object template and selectors → managed-cluster configuration-policy controller → compare and optionally remediate → compliance event`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: ConfigurationPolicy
metadata:
  name: require-owner-label
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

```bash
oc describe policy <name> -n <namespace>
oc get configurationpolicies.policy.open-cluster-management.io -A
```

**Risks:** destructive mustonlyhave, template errors, insufficient RBAC, and repeated remediation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 12. PolicyGenerator, Kustomize, PolicySets, and Policy-as-Code

## Q221. What is the RHACM PolicyGenerator Kustomize plugin?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q222. How do you enable PolicyGenerator in Kustomize?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q223. How does PolicyGenerator convert manifests into policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q224. How do policy defaults reduce repetition?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q225. How do placement rules enter PolicyGenerator output?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q226. How do PolicySets enter generated output?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q227. How do you organize policy manifests by environment?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q228. How do you generate configuration policies from Operator manifests?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q229. How do you include raw policy YAML?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q230. How do you manage policy severity and remediation defaults?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q231. How do you use Kustomize overlays for policy promotion?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q232. How do you validate generated policies before deployment?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q233. How do you prevent generated-name collisions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q234. How do you handle Secrets in policy repositories?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q235. How do you use Argo CD to deploy generated policies?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q236. How do you introduce Inform before Enforce?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q237. How do you test policies in a canary cluster set?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q238. How do you roll back a bad generated-policy release?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q239. How do you troubleshoot Kustomize plugin failures?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
oc apply --dry-run=server -f rendered.yaml
kustomize build --enable-alpha-plugins ./policies
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q240. How do you create an enterprise policy-as-code repository?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **generate and promote policies through Kustomize and GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git manifests and PolicyGenerator → rendered Policy, PolicySet, and Placement → Argo CD reconciliation → fleet compliance`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: policy.open-cluster-management.io/v1
kind: PolicyGenerator
metadata:
  name: platform-policies
policyDefaults:
  namespace: policies
  remediationAction: inform
policies:
  - name: baseline
    manifests:
      - path: manifests/
```

```bash
kustomize build --enable-alpha-plugins ./policies
oc apply --dry-run=server -f rendered.yaml
```

**Risks:** plugin failures, generated-name collision, secret exposure, and bad policy fan-out. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 13. Compliance Operator Across Multiple Clusters

## Q241. Explain the OpenShift Compliance Operator architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q242. How does RHACM distribute Compliance Operator resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q243. What is a ComplianceSuite?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q244. What is a ComplianceScan?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q245. What is a ScanSetting?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q246. What is a ScanSettingBinding?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q247. What is a ProfileBundle?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q248. What is a TailoredProfile?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q249. What is a ComplianceCheckResult?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q250. What is a ComplianceRemediation?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q251. How do you install the Compliance Operator through governance?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller,...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q252. How do you schedule compliance scans across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q253. How do you aggregate compliance status through RHACM?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q254. How do you apply remediations safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q255. How do MachineConfig remediations affect nodes?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q256. How do you customize profiles?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q257. How do you troubleshoot scans stuck Running?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q258. How do you troubleshoot ProfileBundle errors?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q259. How do you troubleshoot remediation that does not apply?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q260. How do you create an enterprise multicluster compliance workflow?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **operate Compliance Operator scans and remediation across clusters**. Identify the hub resource, owning controller,...

**Flow:** `governance deployment → ComplianceSuite and scans → check results and remediations → RHACM compliance aggregation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, stale profiles, unsafe node remediation, and inconsistent schedules. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 14. Governance Troubleshooting, Events, Templates, and Add-ons

## Q261. How do you troubleshoot a policy stuck Unknown?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q262. How do you troubleshoot a policy stuck Pending?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q263. How do you troubleshoot incorrect NonCompliant status?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q264. How do you inspect replicated policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q265. How do you inspect configuration-policy controller logs?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q266. How do you inspect governance-policy-framework logs?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q267. How do you inspect policy-propagator logs?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q268. How do you troubleshoot hub-template resolution failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q269. How do you troubleshoot missing hub-template permissions?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q270. How do you troubleshoot invalid object templates?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q271. How do you troubleshoot policy dependencies?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q272. How do you troubleshoot policy-set placement?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q273. How do you troubleshoot governance add-on availability?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q274. How do you force policy reevaluation safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q275. How do you identify policy event history?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q276. How do you troubleshoot excessive policy events?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q277. How do you distinguish framework failure from target-resource failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get policies -A
oc get events -A --sort-by=.lastTimestamp | grep -i policy
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q278. How do you collect a governance diagnostic bundle?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q279. How do you preserve evidence before deleting a policy?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q280. How do you create an L3 governance troubleshooting flow?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **diagnose policy propagation and evaluation**. Identify...

**Flow:** `root policy and placement → replication → add-on and template resolution → target resource compare or remediation → hub status`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp | grep -i policy
oc get policies -A
```

**Risks:** deleting evidence, wrong controller logs, stale events, and target-resource failure mistaken for framework failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 15. Observability Architecture, Installation, and Object Storage

## Q281. Explain RHACM observability architecture.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q282. What is the MultiClusterObservability resource?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. MultiClusterObservability enables the hub observability stack and requires object-storage configuration for long-term metric data.

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q283. How do observatorium components participate in observability?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q284. How do managed-cluster collectors work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q285. How does Thanos support long-term metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q286. Why is object storage required?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q287. Which object-storage providers are supported conceptually?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q288. How do object-storage credentials enter RHACM?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q289. How do you create the thanos-object-storage Secret?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q290. How do you enable observability?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q291. How do you monitor MultiClusterObservability status?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. MultiClusterObservability enables the hub observability stack and requires object-storage configuration for long-term metric data.

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q292. How do you size hub observability components?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q293. How do you size managed-cluster collectors?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q294. How do proxies affect metric transport?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get pods -n open-cluster-management-observability
oc get multiclusterobservability -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q295. How do custom CAs affect object-storage access?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q296. How do you validate object-storage connectivity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q297. How do you troubleshoot observability installation failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**....

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q298. How do you troubleshoot invalid object-storage configuration?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**....

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q299. How do you disable observability safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q300. How do you create an enterprise observability deployment standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **deploy fleet observability with object storage**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `MultiClusterObservability and credentials → hub observatorium and Thanos components → managed collectors → object storage and Grafana`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: observability.open-cluster-management.io/v1beta2
kind: MultiClusterObservability
metadata:
  name: observability
spec:
  observabilityAddonSpec:
    enableMetrics: true
```

```bash
oc get multiclusterobservability -n open-cluster-management-observability
oc get pods -n open-cluster-management-observability
```

**Risks:** bad object-store config, missing CA trust, insufficient capacity, and metric transport failure. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 16. Observability Customization, Metrics, Alerts, Grafana, and Retention

## Q301. How do you customize metric allowlists?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q302. How do you create a custom-allowlist ConfigMap?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q303. How do you collect user-workload metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q304. How do you add custom recording rules?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q305. How do you add custom alerting rules?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q306. How do you route fleet alerts?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q307. How do you customize Grafana dashboards?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q308. How do you use managed-cluster labels in dashboards?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q309. How do you monitor virtual machine metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q310. How do you query fleet metrics through Thanos?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q311. How do retention and object-storage lifecycle interact?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q312. How do you reduce high-cardinality metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q313. How do you monitor collector backlog?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q314. How do you monitor object-storage consumption?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q315. How do you troubleshoot missing cluster metrics?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q316. How do you troubleshoot stale Grafana data?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q317. How do you troubleshoot alert-delivery failures?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q318. How do you prevent observability from overloading clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q319. How do you define multicluster SLIs and SLOs?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get prometheusrules -A
oc get cm -n open-cluster-management-observability
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q320. How do you create an enterprise observability operations model?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **customize fleet metrics, dashboards, alerts, and retention**. Identify the hub resource, owning controller,...

**Flow:** `allowlist and rule configuration → managed collection → Thanos storage and query → Grafana and Alertmanager`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get cm -n open-cluster-management-observability
oc get prometheusrules -A
```

**Risks:** high cardinality, missing metrics, alert storms, stale dashboards, and storage growth. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 17. Git, Kustomize, Repository Design, and Multicluster Configuration

## Q321. How do you use Git effectively for RHACM administration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q322. How do branches, tags, and commits support fleet changes?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q323. How do you review changes with pull requests?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q324. How do you revert a bad commit?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q325. How do you structure repositories for hub configuration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q326. How do you structure repositories for cluster configuration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q327. How do you separate governance and application repositories?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q328. How do Kustomize bases and overlays work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q329. How do patchesStrategicMerge and JSON patches differ?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller,...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q330. How do name prefixes and suffixes affect resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q331. How do ConfigMapGenerator and SecretGenerator work?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q332. How do you pin remote Kustomize bases?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q333. How do you validate rendered resources?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q334. How do you prevent credentials from entering Git?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q335. How do you sign commits or tags?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q336. How do you promote configuration across environments?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q337. How do you manage cluster-specific overlays?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q338. How do you resolve Kustomize merge conflicts?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
git log --oneline -20
kustomize build overlays/prod | oc apply --dry-run=server -f -
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q339. How do you audit Git-based fleet changes?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q340. How do you create an enterprise multicluster Git standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **manage multicluster resources with Git and Kustomize**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `Git commit and review → Kustomize render → validated OpenShift resources → GitOps reconciliation and audit`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
kustomize build overlays/prod | oc apply --dry-run=server -f -
git log --oneline -20
```

**Risks:** unpinned bases, credential commits, merge errors, and environment drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels, ManagedClusterSetBinding,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 18. OpenShift GitOps Operator, Argo CD, and Managed-Cluster Import

## Q341. How do you deploy the OpenShift GitOps Operator?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q342. How do OperatorGroup and Subscription resources affect GitOps installation?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q343. How do Argo CD instances relate to RHACM?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q344. How do you import managed clusters into Argo CD?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q345. How does managed-cluster registration create Argo CD cluster Secrets?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q346. How do cluster selectors choose imported clusters?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q347. How do ManagedServiceAccount and token rotation support GitOps access?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q348. How do you restrict which clusters an Argo CD instance can manage?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q349. How do you configure Git repository credentials?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q350. How do you configure private CA trust for Git repositories?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q351. How do you create an Argo CD Application for fleet configuration?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q352. How do ApplicationSets target managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q353. How do RHACM Placement decisions integrate with ApplicationSet?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q354. How do you deploy policies through GitOps?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q355. How do you troubleshoot a managed cluster missing from Argo CD?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**....

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q356. How do you troubleshoot expired managed-service-account tokens?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**....

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q357. How do you troubleshoot repository authentication failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**....

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q358. How do you troubleshoot GitOps reconciliation drift?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**....

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q359. How do you remove a managed cluster from Argo CD safely?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get argocds -A
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q360. How do you create an enterprise RHACM-GitOps integration standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **integrate OpenShift GitOps and managed clusters**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `GitOps Operator and Argo CD → managed-service-account and cluster Secret → Application or ApplicationSet → managed-cluster sync`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: fleet-app
  namespace: openshift-gitops
spec:
  generators:
    - clusterDecisionResource:
        configMapRef: acm-placement
        labelSelector:
          matchLabels:
            cluster.open-cluster-management.io/placement: production
  template:
    metadata:
      name: '{{name}}-fleet-app'
    spec:
      project: default
```

```bash
oc get secrets -n openshift-gitops -l argocd.argoproj.io/secret-type=cluster
oc get argocds -A
```

**Risks:** token expiry, repository trust failure, overly broad cluster access, and reconciliation drift. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 19. Multicluster Application Lifecycle, Placement, and GitOps Delivery

## Q361. How does RHACM manage applications across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q362. How do ApplicationSet and Placement integrate?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q363. How do PlacementDecision resources feed GitOps generators?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q364. How do cluster labels control application targeting?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q365. How do you deploy an application to a cluster set?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q366. How do you implement dev, test, and production promotion?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q367. How do you target different values by region?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller,...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q368. How do you manage Helm applications through GitOps?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q369. How do you manage Kustomize applications through GitOps?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q370. How do you manage application Secrets safely?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q371. How do you pause a fleet rollout?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q372. How do you perform a canary cluster rollout?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q373. How do you prevent a bad commit from affecting all clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q374. How do you observe fleet application health?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q375. How do you roll back an application across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q376. How do you handle a disconnected managed cluster?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q377. How do you troubleshoot ApplicationSet generation failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**....

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q378. How do you troubleshoot application sync failure on one cluster?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**....

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q379. How do you decommission a multicluster application?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get placements,placementdecisions -A
oc get applicationsets,applications -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q380. How do you create an enterprise application-lifecycle standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **deliver applications across cluster sets with GitOps**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `Git application and Placement → PlacementDecision-based generator → Argo CD Application → cluster sync and health`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get applicationsets,applications -A
oc get placements,placementdecisions -A
```

**Risks:** bad commit fan-out, incorrect labels, disconnected clusters, and incomplete rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 20. OpenShift Virtualization Operator Deployment with RHACM and GitOps

## Q381. Explain OpenShift Virtualization architecture in a multicluster fleet.

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q382. How does the HyperConverged Operator manage virtualization components?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q383. How do you deploy OpenShift Virtualization through RHACM governance?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q384. How do you deploy OpenShift Virtualization through GitOps?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q385. How do Subscription and HyperConverged resources interact?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q386. How do you select clusters eligible for virtualization?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q387. How do you validate hardware virtualization capabilities?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q388. How do you enforce virtualization namespace configuration?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q389. How do you configure storage classes for virtual machines?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q390. How do you configure network attachment definitions?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q391. How do you manage DataImportCron and DataSource resources?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q392. How do you distribute VM templates across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q393. How do you monitor operator installation across clusters?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q394. How do you upgrade OpenShift Virtualization across a fleet?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q395. How do you troubleshoot a HyperConverged resource stuck Progressing?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q396. How do you troubleshoot CDI or KubeVirt components?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q397. How do you prevent unsupported clusters from receiving virtualization?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q398. How do you manage virtualization policy exceptions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get subscriptions -A | grep -i kubevirt
oc get hyperconverged -A
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q399. How do you audit virtualization configuration drift?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q400. How do you create an enterprise virtualization-operator rollout standard?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **deploy OpenShift Virtualization consistently across clusters**. Identify the hub resource, owning controller,...

**Flow:** `eligible-cluster Placement → Subscription and HyperConverged resources via governance or GitOps → KubeVirt and CDI operands → validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get hyperconverged -A
oc get subscriptions -A | grep -i kubevirt
```

**Risks:** unsupported hardware, storage or network gaps, operator version mismatch, and partial fleet rollout. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 21. Multicluster Virtual Machines, Search, Actions, and Monitoring

## Q401. How does RHACM discover virtual machines across managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q402. How do you search for VMs by cluster, namespace, and status?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q403. How do you view VM and VMI health across clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q404. How do you deploy VM definitions through GitOps?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q405. How do you distribute VM templates?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q406. How do you target VMs to selected clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q407. How do you manage VM labels for fleet operations?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q408. How do you monitor VM CPU and memory metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q409. How do you monitor VM storage and network metrics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q410. How do you identify stopped or failed VMs?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q411. How do you identify VMs using deprecated templates?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q412. How do you enforce VM security policies?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q413. How do you enforce VM backup labels?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q414. How do you perform VM lifecycle actions from RHACM?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q415. How do you troubleshoot a VM missing from Search?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q416. How do you troubleshoot missing VM observability data?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**....

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q417. How do you troubleshoot GitOps-managed VM drift?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**....

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q418. How do you prevent unsafe fleet-wide VM actions?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q419. How do you design multicluster VM ownership boundaries?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters --show-labels
oc get vm,vmi -A
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q420. How do you create an enterprise multicluster virtualization runbook?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **manage and monitor virtual machines across clusters**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `VM definitions and labels → GitOps or cluster API → Search and observability collection → fleet view and controlled action`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get vm,vmi -A
oc get managedclusters --show-labels
```

**Risks:** missing search data, absent metrics, unsafe bulk action, template drift, and unclear ownership. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 22. Backup, Restore, Hub Recovery, and Business Continuity

## Q421. What RHACM resources require backup?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q422. How does the RHACM Backup and Restore operator work?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q423. What is a ClusterBackup resource?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q424. What is a ClusterRestore resource?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q425. How do backup labels control resource inclusion?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q426. How do you configure an object-storage backup location?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q427. How do you protect backup credentials?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q428. How do you schedule hub backups?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q429. How do you restore RHACM to a replacement hub?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q430. How do managed clusters reconnect after hub restore?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q431. How do you preserve managed-cluster import information?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q432. How do GitOps resources participate in backup?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q433. How do governance resources participate in backup?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q434. How do observability data and configuration differ in recovery?

### Answer

Compare the options through API ownership, scope, reconciliation, permissions, failure behavior, and operational lifecycle. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q435. How do you validate a restored hub?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q436. How do you troubleshoot failed backups?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q437. How do you troubleshoot a restore stuck Running?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q438. How do you define hub RTO and RPO?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get clusterbackups,clusterrestores -A
oc get pods -n open-cluster-management-backup
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q439. How do you test hub disaster recovery?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set and...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q440. How do you create an enterprise RHACM business-continuity runbook?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and restore hub management state**. Identify the hub resource, owning controller, managed-cluster agent or...

**Flow:** `backup operator and schedule → selected RHACM resources and credentials → object storage → replacement-hub restore and cluster reconnection`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ClusterBackup
metadata:
  name: scheduled-hub-backup
  namespace: open-cluster-management-backup
spec:
  schedule: '0 2 * * *'
  veleroManagedClustersBackupName: auto-import-resources-schedule
```

```bash
oc get pods -n open-cluster-management-backup
oc get clusterbackups,clusterrestores -A
```

**Risks:** missing resources, exposed credentials, stale import state, untested restore, and incomplete validation. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 23. Networking, Add-ons, Submariner, and Managed-Cluster Connectivity

## Q441. What network paths are required between hub and managed clusters?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q442. How do registration and work agents communicate with the hub?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q443. How do proxies and firewalls affect managed-cluster connectivity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get submarinerconfigs,brokers -A
oc get managedclusteraddons -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q444. How do cluster-proxy add-ons support access?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get submarinerconfigs,brokers -A
oc get managedclusteraddons -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q445. How do managed-service-account add-ons support automation?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get submarinerconfigs,brokers -A
oc get managedclusteraddons -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q446. What is Submariner and when is it used?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q447. How do Broker resources participate in Submariner?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get submarinerconfigs,brokers -A
oc get managedclusteraddons -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q448. How do SubmarinerConfig resources affect deployment?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q449. How does Lighthouse provide service discovery?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q450. How do overlapping CIDRs affect multicluster networking?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q451. How do NAT and private networks affect connectivity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q452. How do custom CA bundles affect add-ons?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q453. How do you monitor add-on availability?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q454. How do you troubleshoot an add-on stuck Unavailable?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q455. How do you troubleshoot agent websocket or TLS errors?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q456. How do you troubleshoot cluster-proxy access?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get submarinerconfigs,brokers -A
oc get managedclusteraddons -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q457. How do you troubleshoot Submariner connectivity?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q458. How do you troubleshoot multicluster service discovery?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q459. How do you restrict add-on deployment by cluster?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q460. How do you create enterprise connectivity standards?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **maintain hub-agent and optional cross-cluster connectivity**. Identify the hub resource, owning controller,...

**Flow:** `DNS, proxy, firewall, TLS, registration, work, and add-on channels → optional Submariner gateways and service discovery`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusteraddons -A
oc get submarinerconfigs,brokers -A
```

**Risks:** blocked websockets, CA mismatch, overlapping CIDRs, add-on failure, and proxy misconfiguration. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 24. Upgrades, Capacity, Security, Troubleshooting, and Platform Operations

## Q461. How do you plan an RHACM hub upgrade?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q462. How do RHACM and OpenShift compatibility affect upgrades?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q463. How do you review release notes and support matrices?

### Answer

Treat this as a multicluster architecture, tenancy, lifecycle, security, observability, GitOps, and recovery decision. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q464. How do you perform pre-upgrade health checks?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q465. How do you back up before upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q466. How do OLM update channels affect upgrades?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q467. How do you validate operands after upgrade?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q468. How do you upgrade managed-cluster add-ons?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q469. How do you monitor hub resource consumption?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q470. How do you size the hub for cluster count and resource count?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q471. How do Search and observability affect capacity?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console and APIs.

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q472. How do governance policies affect API load?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q473. How do you harden the RHACM hub?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q474. How do you protect cluster credentials and Secrets?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q475. How do you collect RHACM diagnostics?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q476. How do you troubleshoot console plug-in failure?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q477. How do you troubleshoot hub API errors?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q478. How do you preserve evidence during incidents?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. During an RHACM incident, preserve the exact Placement, Policy, ApplicationSet, ManagedCluster, add-on, operator, event, log, and...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q479. How do you write an RHACM root-cause analysis?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster agent or add-on,...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get multiclusterhub -A
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q480. How do you create a Corporate/L3 operational methodology?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade, secure, size, observe, and troubleshoot RHACM**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `support matrix and backup → OLM or configuration change → component rollout → health, capacity, security, and workload validation`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc adm must-gather --image=registry.redhat.io/rhacm2/acm-must-gather-rhel9:v2.13
oc get multiclusterhub -A
```

**Risks:** unsupported versions, hub saturation, credential exposure, missing diagnostics, and weak rollback. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

# 25. Corporate L3 EX432 Incident and Design Scenarios

## Q481. All managed clusters become Unknown after a hub certificate change. How do you respond?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**....

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q482. A Placement change targets production policies to development clusters. How do you contain it?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. Placement selects clusters using cluster sets, predicates, claims, tolerations, and prioritizers and writes PlacementDecision resources.

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q483. An Enforce policy removes a required configuration across the fleet. How do you recover?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q484. Search data becomes stale during a security incident. How do you investigate?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. During an RHACM incident, preserve the exact Placement, Policy, ApplicationSet, ManagedCluster, add-on, operator,...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q485. Observability stops receiving metrics from half the fleet. How do you stabilize it?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q486. Object storage becomes unavailable and Thanos components fail. How do you respond?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**....

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q487. A managed-service-account token expires and GitOps loses cluster access. How do you recover?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q488. An ApplicationSet deploys a broken release to many clusters. How do you contain it?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q489. A Compliance Operator remediation reboots nodes unexpectedly. How do you respond?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q490. An RHACM upgrade leaves MultiClusterHub in a degraded state. How do you recover?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q491. A cluster import Secret is exposed. How do you rotate and investigate?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**....

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q492. A ManagedClusterSetBinding grants a team access to unintended clusters. How do you contain it?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. ManagedClusterSet groups clusters into an administrative and placement boundary and requires namespace bindings for consumers. ManagedCluster is the hub representation of a registered cluster and records...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q493. A hub-cluster outage occurs while managed workloads remain healthy. How do you lead recovery?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q494. Backup restore succeeds but governance policies do not propagate. How do you investigate?

### Answer

Perform this through supported OLM and declarative APIs with a canary cluster set, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q495. Virtual machines disappear from RHACM Search but remain running. How do you troubleshoot?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. RHACM Search indexes managed-cluster resource metadata and exposes RBAC-filtered fleet queries through the console...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q496. OpenShift Virtualization deployment fails on part of the fleet. How do you recover?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**....

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q497. Cluster removal is stuck because of finalizers. How do you resolve it safely?

### Answer

Begin by isolating whether the first failure is on the hub, registration or work agents, an add-on, placement, managed-cluster API, Git repository, object storage, or target workload. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**....

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q498. How do you lead an RHACM production incident bridge?

### Answer

First contain placement, policy, GitOps, credential, or lifecycle impact, preserve hub and managed-cluster evidence, and recover through a canary cluster or cluster set. During an RHACM incident, preserve the exact Placement, Policy, ApplicationSet, ManagedCluster, add-on, operator, event, log, and...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q499. What evidence belongs in an RHACM root-cause analysis?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get managedclusters,placements,placementdecisions,policies -A
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---

## Q500. What should a high-quality EX432 Corporate/L3 runbook contain?

### Answer

Explain this from RHACM hub-controller and managed-cluster agent reconciliation perspectives. This topic belongs to the Corporate/L3 responsibility to **lead multicluster incident containment and recovery**. Identify the hub resource, owning controller, managed-cluster agent or add-on, cluster-set...

**Flow:** `declare scope → pause harmful placement, policy, or GitOps → preserve hub and cluster evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OCP, RHACM, multicluster-engine, GitOps, observability, and virtualization versions plus resource conditions and recent changes.
2. Inspect the owning controller, agents, add-ons, labels, cluster-set bindings, PlacementDecisions, RBAC, Events, and logs.
3. Test the smallest reversible correction on one managed cluster or canary cluster set.
4. Validate compliance, Search, metrics, GitOps, virtualization, persistence, and the real workload.
5. Correct Git, Kustomize, OLM, placement, RBAC, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacm-change-record
  namespace: open-cluster-management
data:
  owner: platform-team
  rollout: canary-cluster-set
  validation: conditions-events-and-workload
```

```bash
oc get managedclusters,placements,placementdecisions,policies -A
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide damage, credential exposure, hub outage confusion, evidence loss, and ownerless actions. Also verify OCP and RHACM compatibility, cluster acceptance, agent and add-on conditions, cluster labels,...

**Verify:** Confirm the exact hub and managed-cluster versions, resource generation and conditions, controller logs, Events, Search results, policy compliance, Argo CD status, observability metrics, virtualization...

---
