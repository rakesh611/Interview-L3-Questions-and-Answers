# OpenShift Advanced Cluster Security (EX430) - 500 Corporate L3 Interview Questions and Answers

> Git-compatible edition with exactly 500 questions across 25 domains.  
> Exam alignment: EX430 is based on RHACS 4.6. Production context includes RHACS 4.10.5 concepts where clearly marked.  
> UTF-8, Unix line endings, balanced YAML and Bash fences, and a simple ASCII filename.  
> Independent study material, not an official exam dump.

## Corporate/L3 Method

Identify the lifecycle stage, Central or secured-cluster component, policy, image, integration, and workload. Trace Operator, Central DB, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or backup flow. Preserve CRs, conditions, logs, violations, vulnerability and runtime evidence, and recent changes. Test on one cluster or scope, validate the real workload, and persist the correction.

## Table of Contents

- [01. RHACS Fundamentals, Architecture, and Security Lifecycle](#01-rhacs-fundamentals-architecture-and-security-lifecycle)
- [02. Operator Installation, Subscription, Central CR, and Initial Deployment](#02-operator-installation-subscription-central-cr-and-initial-deployment)
- [03. Central, Central DB, Scanner, Scanner V4, and Component Operations](#03-central-central-db-scanner-scanner-v4-and-component-operations)
- [04. SecuredCluster, Sensor, Collector, Admission Control, and Compliance](#04-securedcluster-sensor-collector-admission-control-and-compliance)
- [05. Cluster Registration, Init Bundles, CRS, Certificates, and Import](#05-cluster-registration-init-bundles-crs-certificates-and-import)
- [06. Vulnerability Management Architecture, CVEs, CVSS, and Data Sources](#06-vulnerability-management-architecture-cves-cvss-and-data-sources)
- [07. Image Scanning, Registries, Registry Integrations, and Scan Troubleshooting](#07-image-scanning-registries-registry-integrations-and-scan-troubleshooting)
- [08. Node, Platform, OpenShift, and Virtual Machine Vulnerabilities](#08-node-platform-openshift-and-virtual-machine-vulnerabilities)
- [09. Vulnerability Exceptions, Deferrals, False Positives, Reports, and Notifications](#09-vulnerability-exceptions-deferrals-false-positives-reports-and-notifications)
- [10. Risk Dashboard, Collections, Filters, Search, and Remediation Prioritization](#10-risk-dashboard-collections-filters-search-and-remediation-prioritization)
- [11. Security Policies, Criteria, Lifecycle Stages, and Default Policies](#11-security-policies-criteria-lifecycle-stages-and-default-policies)
- [12. Deploy-Time Policies, Admission Controller, Enforcement, and Bypass Prevention](#12-deploy-time-policies-admission-controller-enforcement-and-bypass-prevention)
- [13. Runtime Policies, Process Baselines, Violations, and Incident Response](#13-runtime-policies-process-baselines-violations-and-incident-response)
- [14. Network Graph, Flows, Baselines, Listening Endpoints, and Segmentation](#14-network-graph-flows-baselines-listening-endpoints-and-segmentation)
- [15. Build-Time Policy, roxctl, CI/CD, Image Check, and Deployment Check](#15-build-time-policy-roxctl-cicd-image-check-and-deployment-check)
- [16. Compliance Architecture, Standards, Controls, and Reporting](#16-compliance-architecture-standards-controls-and-reporting)
- [17. OpenShift Compliance Operator, Tailored Profiles, and RHACS Integration](#17-openshift-compliance-operator-tailored-profiles-and-rhacs-integration)
- [18. Authentication, OIDC, Groups, Roles, Permission Sets, and Access Scopes](#18-authentication-oidc-groups-roles-permission-sets-and-access-scopes)
- [19. Notifiers, SIEM, Email, Webhooks, Jira, and Third-Party Integrations](#19-notifiers-siem-email-webhooks-jira-and-third-party-integrations)
- [20. Backup, Restore, Object Storage, s3cmd, and Disaster Recovery](#20-backup-restore-object-storage-s3cmd-and-disaster-recovery)
- [21. Monitoring, Metrics, Logs, Health, Diagnostics, and Support](#21-monitoring-metrics-logs-health-diagnostics-and-support)
- [22. Scalability, High Availability, Performance, and Capacity Planning](#22-scalability-high-availability-performance-and-capacity-planning)
- [23. Security Hardening, Certificates, NetworkPolicy, Secrets, and Audit](#23-security-hardening-certificates-networkpolicy-secrets-and-audit)
- [24. Upgrades, Compatibility, Migration, Rollback, and Lifecycle Management](#24-upgrades-compatibility-migration-rollback-and-lifecycle-management)
- [25. Corporate L3 EX430 Incident and Design Scenarios](#25-corporate-l3-ex430-incident-and-design-scenarios)

---

# 01. RHACS Fundamentals, Architecture, and Security Lifecycle

## Q001. What problems does Red Hat Advanced Cluster Security solve?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS is a Kubernetes-native security platform covering vulnerability management, configuration risk, deploy-time admission control, runtime detection, network analysis, and compliance.

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q002. Explain RHACS architecture and its major components.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q003. How does RHACS protect build, deploy, and runtime stages?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q004. How do Central and secured-cluster components interact?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q005. What data is stored centrally and what remains on secured clusters?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q006. How does RHACS differ from an image scanner?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q007. How does RHACS differ from OpenShift built-in security controls?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q008. How does RHACS differ from a SIEM platform?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q009. How does RHACS differ from Kubernetes admission control alone?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q010. How do declarative resources and RHACS API objects differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q011. How do security teams and platform teams divide RHACS ownership?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q012. How do application teams consume RHACS findings?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q013. How does RHACS handle multiple secured clusters?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q014. How do disconnected clusters affect architecture?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q015. How do failure domains affect Central design?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q016. How does RHACS support non-OpenShift Kubernetes clusters?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q017. How do build-time and runtime evidence differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q018. How do risk, violations, vulnerabilities, and compliance differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q019. How do you assess overall RHACS platform health?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q020. What are Corporate/L3 RHACS administrator responsibilities?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate RHACS across build, deploy, and runtime security stages**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift and CI events → secured-cluster components → Central data processing → policy, vulnerability, risk, network, and compliance views → response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get central,securedcluster -A
```

**Risks:** treating RHACS as only a scanner, unclear enforcement ownership, stale cluster data, and Central dependency assumptions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 02. Operator Installation, Subscription, Central CR, and Initial Deployment

## Q021. How do you install the RHACS Operator from OperatorHub?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q022. Which namespace should host the RHACS Operator?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q023. Which namespace should host Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q024. How do Subscription, OperatorGroup, InstallPlan, and CSV resources interact?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q025. How do automatic and manual install-plan approval differ?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q026. How do you choose an RHACS update channel?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q027. How do you create a Central custom resource?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q028. How do Central services, routes, and storage get created?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q029. How do you configure Central exposure through an OpenShift Route?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q030. How do passthrough and reencrypt routes differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q031. How do you configure Central database storage?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q032. How do node selectors, tolerations, and affinity affect Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q033. How do proxies affect Central installation?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q034. How do custom certificate authorities affect installation?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q035. How do disconnected registries affect installation?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q036. How do you mirror RHACS operator catalogs and images?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q037. How do you validate Operator and Central readiness?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q038. How do you troubleshoot a CSV in Failed state?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q039. How do you troubleshoot a Central CR stuck in error?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get subscription,installplan,csv -n rhacs-operator
oc get central -n stackrox -o yaml
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q040. How do you create an enterprise RHACS installation runbook?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deploy the Operator and Central through supported OpenShift resources**. Identify the Central or...

**Flow:** `OperatorHub catalog → Subscription and CSV → RHACS Operator → Central CR → Central, database, Scanner, route, and storage`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: Central
metadata:
  name: stackrox-central-services
  namespace: stackrox
spec:
  central:
    exposure:
      route:
        enabled: true
  scanner:
    scannerComponent: Enabled
```

```bash
oc get central -n stackrox -o yaml
oc get subscription,installplan,csv -n rhacs-operator
```

**Risks:** wrong namespace, channel or InstallPlan failure, missing storage, proxy and CA issues, and mirrored-image gaps. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 03. Central, Central DB, Scanner, Scanner V4, and Component Operations

## Q041. Explain the responsibilities of Central.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q042. Explain the responsibilities of Central DB.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q043. How does Scanner process image metadata?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability services**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q044. How does Scanner DB provide vulnerability definitions?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q045. How does Scanner V4 differ operationally from earlier scanning?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability services**. Identify the Central or secured-cluster component,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q046. How do Scanner V4 indexer and matcher functions differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability services**. Identify the Central or secured-cluster component,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q047. How do Central and Scanner communicate?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q048. How does Central ingest secured-cluster data?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q049. How do Central API and UI access the same data?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q050. How do you monitor Central database health?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q051. How do you monitor Scanner health?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability services**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q052. How do you monitor vulnerability feed freshness?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q053. How do you scale Central components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q054. How do you dedicate nodes to Central components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q055. How do you troubleshoot Central CrashLoopBackOff?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q056. How do you troubleshoot Central DB startup failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m
oc get pods,pvc,svc,route -n stackrox
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q057. How do you troubleshoot Scanner unavailable errors?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q058. How do you troubleshoot vulnerability-feed update failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q059. How do you collect Central diagnostics?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q060. How do you create an enterprise component-operations standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **operate Central, database, Scanner, and vulnerability services**. Identify the Central or secured-cluster component,...

**Flow:** `secured-cluster and registry data → Central API and processing → Central DB and Scanner services → UI, API, reports, and policies`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc,svc,route -n stackrox
oc logs -n stackrox deploy/central --since=30m
```

**Risks:** database outage, stale feeds, Scanner unavailability, insufficient storage, and unsupported manual edits. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 04. SecuredCluster, Sensor, Collector, Admission Control, and Compliance

## Q061. What is the SecuredCluster custom resource?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q062. How does Sensor communicate with Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API. Sensor watches...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q063. How does Collector observe runtime activity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q064. How does the admission controller enforce deploy-time policy?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. The RHACS admission controller evaluates supported workload requests against deploy-time policies and can reject noncompliant deployments.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q065. How does the compliance component collect configuration data?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q066. How do Scanner components run on secured clusters?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q067. How do secured-cluster services authenticate to Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q068. How do secured-cluster components use certificates?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q069. How do DaemonSets support node-level visibility?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q070. How do SCCs affect secured-cluster components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q071. How do node selectors and tolerations affect secured-cluster components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q072. How do proxies affect secured-cluster connectivity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q073. How do custom CAs affect secured-cluster communication?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q074. How do you validate Sensor connectivity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Sensor watches Kubernetes resources on a secured cluster, communicates with Central, and coordinates local RHACS components.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q075. How do you validate Collector health on every node?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q076. How do you validate admission-control readiness?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q077. How do you troubleshoot Sensor disconnected?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Sensor watches Kubernetes resources on a secured cluster, communicates with Central, and coordinates local RHACS...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get securedcluster -n stackrox -o yaml
oc get pods -n stackrox -o wide
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q078. How do you troubleshoot Collector missing from nodes?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q079. How do you troubleshoot compliance component failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q080. How do you create an enterprise secured-cluster standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **operate Sensor, Collector, admission control, and compliance components**. Identify the Central or secured-cluster...

**Flow:** `cluster API and node events → Sensor and Collector → Central plus local admission and compliance functions → status and enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: platform.stackrox.io/v1alpha1
kind: SecuredCluster
metadata:
  name: stackrox-secured-cluster-services
  namespace: stackrox
spec:
  clusterName: prod-cluster
  centralEndpoint: central-stackrox.apps.example.com:443
  admissionControl:
    listenOnCreates: true
    listenOnUpdates: true
  perNode:
    collector:
      collection: CORE_BPF
```

```bash
oc get pods -n stackrox -o wide
oc get securedcluster -n stackrox -o yaml
```

**Risks:** agent disconnect, missing node coverage, SCC restrictions, certificate failure, and admission outage. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 05. Cluster Registration, Init Bundles, CRS, Certificates, and Import

## Q081. How do you add a secured cluster to Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q082. What is an RHACS init bundle?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q083. Which Secrets are generated from an init bundle?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q084. How do you generate an init bundle with roxctl?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed. roxctl is the RHACS command-line...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q085. How do you apply an init bundle safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q086. How do you revoke an init bundle?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q087. How do you rotate secured-cluster certificates?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q088. How does the cluster registration secret method differ from init bundles?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. RHACS 4.10 introduced cluster registration secrets as a newer secure bootstrapping method for registering secured clusters. An init bundle contains bootstrap certificates and...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q089. Why is the cluster registration secret method preferred in newer RHACS releases?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS 4.10 introduced cluster registration secrets as a newer secure bootstrapping method for registering secured clusters.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q090. How do you protect bootstrap credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q091. How do you name secured clusters consistently?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q092. How do you import a cluster through the Operator?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q093. How do you import a cluster with Helm or roxctl-generated manifests?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q094. How do you validate the cluster registration in Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q095. How do you re-register a secured cluster safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q096. How do you remove a secured cluster from Central?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q097. How do you troubleshoot invalid init-bundle credentials?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q098. How do you troubleshoot certificate trust failures?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q099. How do you investigate an exposed registration Secret?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q100. How do you create an enterprise cluster-registration workflow?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **bootstrap and maintain secured-cluster trust**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Central-generated init bundle or registration secret → cluster Secrets and SecuredCluster CR → certificate-authenticated Sensor connection → cluster registration`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central init-bundles generate lab --output-secrets init-bundle.yaml
oc get secrets -n stackrox | grep -E 'sensor|collector|admission'
```

**Risks:** exposed bootstrap credentials, invalid certificates, duplicate identity, stale import data, and unsafe revocation. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 06. Vulnerability Management Architecture, CVEs, CVSS, and Data Sources

## Q101. Explain RHACS vulnerability-management architecture.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q102. What is a CVE and how does RHACS represent it?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q103. How do CVSS scores affect prioritization?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q104. How do severity categories differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q105. How does fix availability affect remediation priority?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q106. How do image, node, platform, and virtual-machine vulnerabilities differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q107. How do vulnerability data sources affect findings?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q108. How do vendor advisories differ from NVD data?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q109. How do package managers affect component detection?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q110. How do operating-system and language-package vulnerabilities differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q111. How do image layers affect vulnerability ownership?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q112. How do standardized base images improve remediation ownership?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q113. How does image age affect risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q114. How do exploitability and business context affect prioritization?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q115. How does RHACS calculate deployment risk?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component,...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q116. How do you identify newly introduced CVEs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q117. How do you identify critical vulnerabilities with fixes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q118. How do you troubleshoot missing CVE data?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **manage vulnerability evidence and prioritization**. Identify...

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q119. How do you validate vulnerability data freshness?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q120. How do you create an enterprise vulnerability taxonomy?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `image, node, platform, or VM inventory → Scanner and vulnerability sources → components and CVEs → risk, fixability, ownership, and remediation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** stale feeds, incomplete coverage, severity-only prioritization, base-image ambiguity, and missing vendor fixes. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 07. Image Scanning, Registries, Registry Integrations, and Scan Troubleshooting

## Q121. How does RHACS scan container images?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q122. How do registry integrations provide image metadata and credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q123. How do you integrate an external private registry?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q124. How do you integrate Quay with RHACS?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q125. How do you integrate Red Hat registries?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q126. How do you integrate generic Docker v2 registries?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q127. How do you scope registry credentials securely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q128. How do image pull Secrets affect secured-cluster scanning?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q129. How does RHACS scan images before deployment?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q130. How does RHACS scan images already running?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q131. How do image tags and digests affect scan identity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q132. How do manifest lists and multi-architecture images affect scanning?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q133. How do you force or trigger an image scan?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q134. How do you inspect image components and layers?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q135. How do you troubleshoot registry authentication failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q136. How do you troubleshoot registry TLS errors?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image registry.example.com/app@sha256:...
oc get secrets -A | grep -i docker
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q137. How do you troubleshoot image not found?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q138. How do you troubleshoot scan timeout?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q139. How do you prevent registry integrations from becoming overprivileged?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q140. How do you create an enterprise registry-integration standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **integrate registries and scan images securely**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `image reference and registry integration or pull Secret → manifest and layer retrieval → Scanner processing → component and CVE result`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get secrets -A | grep -i docker
roxctl image scan --image registry.example.com/app@sha256:...
```

**Risks:** overprivileged credentials, TLS failure, mutable tags, rate limits, scan timeout, and unsupported registry behavior. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 08. Node, Platform, OpenShift, and Virtual Machine Vulnerabilities

## Q141. How does RHACS scan node vulnerabilities?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q142. How do node operating-system packages affect findings?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q143. How do OpenShift platform vulnerabilities differ from workload vulnerabilities?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q144. How does RHACS identify cluster-level security risks?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q145. How do kernel and runtime vulnerabilities affect node risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q146. How do you compare vulnerabilities across nodes?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q147. How do you identify nodes requiring urgent remediation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q148. How do you coordinate node remediation with MachineConfigPools?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q149. How do you handle findings without vendor fixes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q150. How do you validate node scan coverage?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q151. How do you troubleshoot node scan gaps?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**....

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q152. How do unsupported nodes affect visibility?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q153. How does RHACS 4.10 support VM vulnerability scanning conceptually?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q154. What role does the VM agent play in VM scanning?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q155. How do VM and container vulnerability workflows differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get machineconfigpools
oc get nodes -o wide
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q156. How do you prioritize platform versus application remediation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q157. How do you report platform vulnerabilities to operations teams?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q158. How do you verify remediation after node updates?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q159. How do you avoid rebooting too many nodes simultaneously?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q160. How do you create enterprise node and platform remediation standards?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **manage node, platform, and VM vulnerability remediation**. Identify the Central or secured-cluster component,...

**Flow:** `node or VM package inventory → vulnerability matching → fleet comparison → controlled patch, reboot, or workload migration → rescan`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes -o wide
oc get machineconfigpools
```

**Risks:** coverage gaps, disruptive remediation, no-fix findings, unsupported nodes, and simultaneous reboot risk. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 09. Vulnerability Exceptions, Deferrals, False Positives, Reports, and Notifications

## Q161. How does vulnerability deferral work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q162. How does false-positive marking differ from deferral?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q163. What justification should accompany an exception?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q164. How do expiration dates govern vulnerability exceptions?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q165. How do you approve or deny exception requests?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q166. How do you review expiring exceptions?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q167. How do you revoke an exception?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q168. How do you prevent permanent risk acceptance?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q169. How do you generate vulnerability reports?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q170. How do report scopes and collections work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q171. How do scheduled reports work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q172. How do email and notifier integrations deliver reports?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q173. How do you report vulnerabilities by namespace or application?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q174. How do you report fixable critical CVEs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q175. How do you export vulnerability data through the API?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q176. How do you troubleshoot empty reports?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q177. How do you troubleshoot scheduled-report delivery failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q178. How do you audit exception and report changes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern vulnerability exceptions and reporting**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image scan --image "$IMAGE" --output json > scan.json
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q179. How do you define vulnerability-management SLAs?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q180. How do you create an enterprise vulnerability-governance process?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `finding and business justification → deferral or false-positive workflow → approval and expiry → report and notification → review`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl image scan --image "$IMAGE" --output json > scan.json
```

**Risks:** permanent exceptions, weak evidence, broad scope, failed reports, and unowned remediation SLAs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 10. Risk Dashboard, Collections, Filters, Search, and Remediation Prioritization

## Q181. How does the RHACS risk view prioritize deployments?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q182. Which factors influence deployment risk?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q183. How do policy violations affect risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q184. How do vulnerabilities affect risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q185. How do exposed ports and network behavior affect risk?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**....

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q186. How do secrets and service-account permissions affect risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q187. What are RHACS collections?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q188. How do collections scope dashboards, reports, and policies?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q189. How do search syntax and filters work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q190. How do you filter by cluster, namespace, deployment, image, and CVE?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q191. How do you identify internet-exposed high-risk deployments?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**....

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q192. How do you identify privileged workloads with critical CVEs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q193. How do you identify deployments with excessive service-account permissions?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q194. How do you prioritize remediation using business context?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q195. How do you track risk reduction over time?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q196. How do you prevent risk scores from replacing engineering judgment?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q197. How do you troubleshoot missing deployment data?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**....

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q198. How do you troubleshoot stale risk information?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**....

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
oc get deployments -A
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q199. How do you create remediation queues for different teams?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q200. How do you create an enterprise risk-review workflow?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **prioritize remediation using RHACS risk and collections**. Identify the Central or secured-cluster component,...

**Flow:** `deployment configuration, vulnerabilities, permissions, exposure, and behavior → risk calculation → scoped collection and query → remediation queue`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get deployments -A
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** score-only decisions, stale data, missing business context, broad collections, and duplicate queues. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 11. Security Policies, Criteria, Lifecycle Stages, and Default Policies

## Q201. What is an RHACS security policy?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q202. How do policy lifecycle stages work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q203. How do build, deploy, and runtime policy stages differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q204. How do policy categories and severity work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q205. How do policy criteria combine?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q206. How do AND and OR policy groups work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q207. How do default policies differ from custom policies?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q208. How do enabled and disabled policies behave?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q209. How do policy scopes and exclusions work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q210. How do collections affect policy scope?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q211. How do policy notifiers work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q212. How do remediation actions differ by lifecycle stage?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q213. How do you clone a default policy safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q214. How do you create a custom policy?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q215. How do you export and import policies?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q216. How do you version policies in Git?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q217. How do you test policies before enforcement?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q218. How do you identify duplicate or conflicting policies?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q219. How do you retire an obsolete policy?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q220. How do you create an enterprise policy-governance standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **govern RHACS security policies across lifecycle stages**. Identify the Central or secured-cluster component,...

**Flow:** `policy criteria, scope, stage, severity, and notifier → build, deploy, or runtime evaluation → violation and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl policy import --file policy.json --endpoint "$ROX_ENDPOINT"
roxctl policy export --all --output policies.zip --endpoint "$ROX_ENDPOINT"
```

**Risks:** unreviewed default changes, conflicting scopes, false positives, broad enforcement, and weak version control. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 12. Deploy-Time Policies, Admission Controller, Enforcement, and Bypass Prevention

## Q221. How does RHACS admission control work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q222. How does OpenShift call the RHACS validating webhook?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q223. Which workload resources can admission control evaluate?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q224. How do CREATE and UPDATE operations differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q225. How do dry-run and enforcement modes differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q226. How do enforce-on-create and enforce-on-update settings differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q227. How do you enable admission control on a secured cluster?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q228. How do you enforce deploy-time policies?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q229. How do you begin with audit-only validation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q230. How do you prevent privileged workload deployment?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q231. How do you prevent deployment of images with critical CVEs?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q232. How do you prevent deployment from untrusted registries?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q233. How do you protect system namespaces from accidental blocking?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q234. How do namespace and object selectors affect webhook scope?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q235. How does webhook failure policy affect cluster availability?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q236. How do you troubleshoot admission webhook timeout?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q237. How do you troubleshoot a valid deployment being blocked?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q238. How do you recover when admission control blocks all workloads?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q239. How do you audit admission-control bypasses?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get validatingwebhookconfigurations | grep stackrox
oc get pods -n stackrox -l app=admission-control
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q240. How do you create an enterprise deploy-time enforcement standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **enforce deploy-time policy without blocking cluster operations**. Identify the Central or secured-cluster component,...

**Flow:** `OpenShift workload request → RHACS validating webhook → policy evaluation using Central or local state → allow or deny → audit`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=admission-control
oc get validatingwebhookconfigurations | grep stackrox
```

**Risks:** webhook timeout, fail-closed outage, system-namespace blocking, stale image scan, and unsafe immediate enforcement. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 13. Runtime Policies, Process Baselines, Violations, and Incident Response

## Q241. How does RHACS detect runtime policy violations?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q242. How does Collector provide process and network evidence?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q243. How do process execution criteria work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q244. How do process names, arguments, and ancestry affect policies?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q245. What is a process baseline?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q246. How are process baselines learned?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q247. How do you lock and unlock a process baseline?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q248. How do you identify anomalous process execution?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q249. How do you detect package-manager execution in containers?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q250. How do you detect interactive shells?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q251. How do you detect cryptocurrency mining behavior?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q252. How do you detect privilege escalation attempts?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q253. How do you detect unexpected network connections?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q254. How do runtime policy notifiers work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q255. How do you inspect a violation timeline?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q256. How do you correlate runtime violations with deployment changes?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component,...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q257. How do you troubleshoot missing runtime events?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**....

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q258. How do you suppress known-safe runtime behavior?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q259. How do you preserve runtime evidence?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox -l app=sensor --since=30m
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q260. How do you create an enterprise runtime-policy standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **detect anomalous processes and behavior at runtime**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `node runtime activity → Collector → Sensor and Central → process baseline and runtime policy → violation timeline and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc logs -n stackrox -l app=sensor --since=30m
```

**Risks:** missing Collector coverage, unlocked baselines, high false-positive volume, weak evidence retention, and alert fatigue. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 14. Network Graph, Flows, Baselines, Listening Endpoints, and Segmentation

## Q261. Explain the RHACS network graph.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. The network graph visualizes observed workload connections and can help create and validate Kubernetes NetworkPolicies.

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q262. How does Collector observe network flows?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q263. How do active and inactive flows differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q264. How do ingress and egress directions appear?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q265. How do namespace and deployment views differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q266. How do you identify unexpected cross-namespace traffic?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q267. How do you identify external network connections?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q268. How do you audit listening endpoints?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q269. How do ports and protocols affect network analysis?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q270. What is a network baseline?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q271. How are network baselines learned?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q272. How do you lock a network baseline?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q273. How do you compare observed traffic with baselines?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q274. How do you generate Kubernetes NetworkPolicies from observed flows?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q275. How do you test generated NetworkPolicies safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q276. How do you prevent accidental network isolation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q277. How do you troubleshoot missing network flows?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q278. How do you troubleshoot Collector network visibility?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q279. How do you use network data during an incident?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. During an RHACS incident, preserve exact policy revisions, violations, cluster status, component logs, runtime...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get pods -n stackrox -l app=collector -o wide
oc get networkpolicies -A
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q280. How do you create an enterprise network-segmentation workflow?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **analyze flows and implement network segmentation**. Identify the Central or secured-cluster component,...

**Flow:** `Collector-observed connections and listening endpoints → network graph and baseline → generated or reviewed NetworkPolicy → controlled enforcement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-api
  namespace: payments
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes: [Ingress]
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

```bash
oc get networkpolicies -A
oc get pods -n stackrox -l app=collector -o wide
```

**Risks:** incomplete observations, locked bad baseline, accidental isolation, hidden external traffic, and CNI differences. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 15. Build-Time Policy, roxctl, CI/CD, Image Check, and Deployment Check

## Q281. How does roxctl support CI/CD integration?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q282. How do you obtain and protect an API token?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q283. How do you configure the Central endpoint for roxctl?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q284. How does `roxctl image scan` work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q285. How does `roxctl image check` work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q286. How does `roxctl deployment check` work?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q287. How do build-time policies differ from deploy-time enforcement?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q288. How do you integrate roxctl with Jenkins?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q289. How do you integrate roxctl with GitLab CI?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q290. How do you integrate roxctl with GitHub Actions?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q291. How do you scan image digests rather than mutable tags?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q292. How do you check rendered Kubernetes manifests?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q293. How do you enforce policy gates without blocking emergency remediation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q294. How do you handle Central unavailability in CI?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q295. How do you prevent CI tokens from leaking?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q296. How do you produce machine-readable output?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q297. How do you troubleshoot roxctl authentication failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration,...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q298. How do you troubleshoot build-policy false positives?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q299. How do you audit CI policy bypasses?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q300. How do you create an enterprise build-time security gate?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **shift RHACS policy and image checks into CI/CD**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `built image and rendered deployment → roxctl scan, image check, and deployment check → policy gate → signed or approved release`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl image check --image "$IMAGE" --endpoint "$ROX_ENDPOINT"
roxctl deployment check --file deployment.yaml --endpoint "$ROX_ENDPOINT"
```

**Risks:** token leakage, Central outage, mutable tags, inconsistent policy versions, and emergency bypass without audit. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 16. Compliance Architecture, Standards, Controls, and Reporting

## Q301. Explain RHACS compliance architecture.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q302. How do RHACS compliance scans differ from vulnerability scans?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q303. Which industry standards can RHACS map conceptually?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q304. How do controls, requirements, and standards relate?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q305. How do cluster, node, namespace, and workload evidence differ?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q306. How do you run an on-demand compliance scan?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q307. How do you scope compliance results by cluster?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q308. How do you identify failing controls?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q309. How do you interpret partial or unavailable evidence?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q310. How do you export compliance reports?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q311. How do you schedule compliance reporting?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q312. How do you customize report scope with collections?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q313. How do you assign compliance remediation owners?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q314. How do you track remediation progress?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q315. How do you prevent compliance status from becoming a checkbox exercise?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q316. How do you troubleshoot compliance data not updating?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q317. How do you troubleshoot missing node evidence?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q318. How do you validate compliance after remediation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
oc get nodes,namespaces
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q319. How do you retain compliance evidence?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy or...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q320. How do you create an enterprise RHACS compliance workflow?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **assess standards and control evidence with RHACS**. Identify the Central or secured-cluster component,...

**Flow:** `cluster, node, namespace, and workload evidence → standard and control mapping → scan results and reports → remediation tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get nodes,namespaces
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** checkbox compliance, stale evidence, broad scope, missing ownership, and report-delivery failure. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 17. OpenShift Compliance Operator, Tailored Profiles, and RHACS Integration

## Q321. How does the OpenShift Compliance Operator complement RHACS?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q322. What is a ComplianceSuite?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q323. What is a ComplianceScan?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q324. What is a ScanSetting?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q325. What is a ScanSettingBinding?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q326. What is a ProfileBundle?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q327. What is a TailoredProfile?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q328. What is a ComplianceCheckResult?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q329. What is a ComplianceRemediation?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q330. How do you deploy the Compliance Operator on a secured cluster?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q331. How do you select a compliance profile?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q332. How do you schedule recurring scans?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q333. How do you create a tailored profile?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q334. How do you exclude unsuitable rules?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q335. How do MachineConfig remediations affect nodes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q336. How do you inspect scan results?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q337. How do you troubleshoot a scan stuck Running?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**....

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q338. How do you troubleshoot ProfileBundle errors?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**....

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q339. How do you apply remediations safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get complianceremediations -A
oc get compliancesuites,compliancescans,compliancecheckresults -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q340. How do you create an enterprise tailored-compliance standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **operate OpenShift Compliance Operator with RHACS workflows**. Identify the Central or secured-cluster component,...

**Flow:** `ProfileBundle and settings → ComplianceSuite and scans → check results and remediations → RHACS or external governance tracking`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: compliance.openshift.io/v1alpha1
kind: ScanSettingBinding
metadata:
  name: cis-scan
  namespace: openshift-compliance
profiles:
  - apiGroup: compliance.openshift.io/v1alpha1
    kind: Profile
    name: ocp4-cis
settingsRef:
  apiGroup: compliance.openshift.io/v1alpha1
  kind: ScanSetting
  name: default
```

```bash
oc get compliancesuites,compliancescans,compliancecheckresults -A
oc get complianceremediations -A
```

**Risks:** long scans, node reboot remediation, profile mismatch, stale results, and unsafe auto-application. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint, agent...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 18. Authentication, OIDC, Groups, Roles, Permission Sets, and Access Scopes

## Q341. Explain RHACS authentication and authorization architecture.

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q342. How do local administrator credentials differ from external authentication?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q343. How do you integrate an OIDC provider?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component,...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q344. How do OIDC claims map to RHACS identities?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q345. How do auth-provider rules assign roles?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q346. How do RHACS roles work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q347. How do permission sets work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q348. How do access scopes work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q349. How do collections differ from access scopes?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q350. How do you create read-only vulnerability access?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q351. How do you delegate policy administration?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q352. How do you restrict users to selected clusters or namespaces?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q353. How do you protect API tokens?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q354. How do you rotate API tokens?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component,...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q355. How do you design break-glass access?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q356. How do you troubleshoot OIDC login failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**....

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q357. How do you troubleshoot incorrect role mapping?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**....

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q358. How do you troubleshoot users seeing unexpected resources?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**....

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q359. How do you audit authentication and authorization changes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central userpki create --service-account ci --role Analyst
roxctl central whoami --endpoint "$ROX_ENDPOINT"
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q360. How do you create an enterprise RHACS access-control model?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **provide federated and least-privilege RHACS access**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `OIDC authentication and claims → auth-provider rules → role, permission set, and access scope → UI or API authorization`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl central whoami --endpoint "$ROX_ENDPOINT"
roxctl central userpki create --service-account ci --role Analyst
```

**Risks:** incorrect claim mapping, excessive access scope, token exposure, local-admin dependence, and missing break-glass controls. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 19. Notifiers, SIEM, Email, Webhooks, Jira, and Third-Party Integrations

## Q361. What are RHACS notifiers?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q362. How do policy violations trigger notifications?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q363. How do email notifier integrations work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q364. How do generic webhook integrations work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q365. How do Slack or Teams-style webhook integrations work conceptually?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q366. How do Jira integrations create tickets?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component,...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q367. How do Splunk or SIEM integrations receive findings?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q368. How do syslog integrations work?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q369. How do notifier scopes and policy selections interact?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q370. How do you protect notifier credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q371. How do you test a notifier?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q372. How do you prevent notification storms?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q373. How do you enrich notifications with cluster and namespace context?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q374. How do you route critical runtime alerts differently from vulnerability reports?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q375. How do you troubleshoot webhook TLS errors?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**....

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q376. How do you troubleshoot notifier authentication failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**....

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q377. How do you troubleshoot missing notifications?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**....

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q378. How do you handle third-party service outages?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q379. How do you audit integration changes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
curl -vk https://notifier.example.com/health
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q380. How do you create an enterprise security-notification strategy?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **deliver actionable findings to third-party systems**. Identify the Central or secured-cluster component,...

**Flow:** `policy violation, vulnerability report, or system event → notifier configuration and scope → webhook, email, SIEM, or ticket → acknowledgement`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc logs -n stackrox deploy/central --since=30m | grep -i notifier
curl -vk https://notifier.example.com/health
```

**Risks:** credential exposure, notification storms, TLS failure, external outage, and missing delivery evidence. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 20. Backup, Restore, Object Storage, s3cmd, and Disaster Recovery

## Q381. What RHACS data must be backed up?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q382. How do Central database backups work conceptually?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q383. How do custom deployment backups differ from declarative configuration backups?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q384. How do you configure object storage for backups?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component,...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q385. How do you protect object-storage credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q386. How do you use s3cmd to inspect an S3-compatible bucket?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q387. How do you test object-storage connectivity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q388. How do you schedule backups?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q389. How do you validate backup completion?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q390. How do you restore Central data?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q391. How do certificates and cluster connections behave after restore?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component,...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q392. How do you restore to a replacement OpenShift cluster?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component,...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q393. How do you preserve the Central endpoint during recovery?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q394. How do secured clusters reconnect after restore?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component,...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q395. How do you validate policies, integrations, and vulnerability data after restore?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q396. How do you troubleshoot a failed backup?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**....

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q397. How do you troubleshoot object-storage TLS or credential errors?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**....

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q398. How do you define RHACS RTO and RPO?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q399. How do you test disaster recovery?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
s3cmd ls s3://rhacs-backups/
oc get pods,pvc -n stackrox
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q400. How do you create an enterprise RHACS backup and restore runbook?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **back up and recover Central data and configuration**. Identify the Central or secured-cluster component,...

**Flow:** `Central data and configuration → scheduled encrypted object-storage backup → restore to Central → secured-cluster reconnection and validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods,pvc -n stackrox
s3cmd ls s3://rhacs-backups/
```

**Risks:** invalid credentials, incomplete backup, changed endpoint, disconnected clusters, untested restore, and stale data. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 21. Monitoring, Metrics, Logs, Health, Diagnostics, and Support

## Q401. Which RHACS components expose health information?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q402. How do you monitor Central health?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q403. How do you monitor Central DB health?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q404. How do you monitor Sensor connectivity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Sensor watches Kubernetes resources on a secured cluster, communicates with Central, and coordinates local RHACS components.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q405. How do you monitor Collector coverage?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q406. How do you monitor admission-controller readiness?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. The RHACS admission controller evaluates supported workload requests against deploy-time policies and can reject noncompliant deployments.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q407. How do you monitor Scanner and vulnerability feeds?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q408. How do you monitor certificate expiration?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q409. How do you monitor policy-violation volume?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q410. How do you monitor secured-cluster status?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q411. How do you inspect RHACS Kubernetes Events?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q412. How do you collect operator logs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q413. How do you collect Central logs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q414. How do you collect secured-cluster logs?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component, lifecycle stage,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q415. How do you use roxctl diagnostics?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. roxctl is the RHACS command-line utility for administrative operations, image and deployment checks, CI integration, policy import or export, and diagnostics.

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q416. How do you create a support bundle?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q417. How do you preserve evidence during an incident?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. During an RHACS incident, preserve exact policy revisions, violations, cluster status, component logs, runtime...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q418. How do you distinguish product failure from OpenShift failure?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
roxctl collector support-packages collect --output-dir support
oc get pods -n stackrox -o wide
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q419. How do you define RHACS SLIs and SLOs?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q420. How do you create an enterprise RHACS observability standard?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **monitor RHACS health, coverage, latency, and component status**. Identify the Central or secured-cluster component,...

**Flow:** `component status, logs, metrics, Events, and diagnostics → dashboards, alerts, support bundle, and response`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pods -n stackrox -o wide
roxctl collector support-packages collect --output-dir support
```

**Risks:** process health without data freshness, missing node coverage, certificate expiry, log loss, and weak SLOs. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 22. Scalability, High Availability, Performance, and Capacity Planning

## Q421. What factors drive RHACS Central sizing?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q422. How do cluster count and deployment count affect Central?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q423. How do image and CVE counts affect Central DB?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q424. How do process and network event rates affect capacity?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q425. How do policy counts affect admission latency?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q426. How do vulnerability reports affect resource use?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q427. How do retention settings affect storage?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q428. How do you scale Central replicas where supported?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q429. How do you size Central DB storage?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q430. How do you size Scanner components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q431. How do you size secured-cluster components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q432. How do admission-controller replicas affect availability?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. The RHACS admission controller evaluates supported workload requests against deploy-time policies and can reject noncompliant deployments.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q433. How do node taints and tolerations support dedicated infrastructure?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q434. How do you identify Central CPU or memory pressure?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q435. How do you identify Central DB storage pressure?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q436. How do you identify Sensor backpressure?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Sensor watches Kubernetes resources on a secured cluster, communicates with Central, and coordinates local RHACS components.

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q437. How do you load-test admission policies safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q438. How do you prevent alert and event storms?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q439. How do you establish capacity headroom?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component, lifecycle stage, scope,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get pvc -n stackrox
oc adm top pods -n stackrox --containers
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q440. How do you create an enterprise RHACS capacity model?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **capacity-plan RHACS Central and secured-cluster workloads**. Identify the Central or secured-cluster component,...

**Flow:** `cluster, deployment, image, CVE, process, flow, and policy volumes → Central, DB, Scanner, Sensor, Collector, and admission resources`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc adm top pods -n stackrox --containers
oc get pvc -n stackrox
```

**Risks:** database growth, Central memory pressure, Sensor backpressure, admission latency, event storms, and inadequate headroom. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 23. Security Hardening, Certificates, NetworkPolicy, Secrets, and Audit

## Q441. How do you harden the RHACS Operator namespace?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q442. How do you harden the Central namespace?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q443. How do you protect Central routes and endpoints?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q444. How do you enforce trusted TLS certificates?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q445. How do you rotate Central and secured-cluster certificates?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q446. How do you protect init bundles and registration Secrets?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. An init bundle contains bootstrap certificates and Secrets used to register secured-cluster components with Central and must be protected and revoked when exposed.

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q447. How do you protect registry credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q448. How do you protect notifier and backup credentials?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q449. How do you protect API tokens?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q450. How do NetworkPolicies protect Central components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API.

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q451. How do NetworkPolicies protect secured-cluster components?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q452. How do SCCs and service accounts affect RHACS security?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q453. How do you restrict administrative API access?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q454. How do you audit policy and integration changes?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q455. How do you verify RHACS image provenance?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q456. How do you handle sensitive data in support bundles?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q457. How do you respond to compromised RHACS credentials?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc get networkpolicies,secrets,serviceaccounts -n stackrox
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q458. How do you threat-model an RHACS deployment?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q459. How do you conduct RHACS security testing safely?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or secured-cluster component,...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q460. How do you create an enterprise RHACS security baseline?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **harden RHACS trust, credentials, network paths, and administrative surfaces**. Identify the Central or...

**Flow:** `trusted Operator images and certificates → protected Central and secured components → least-privilege APIs, Secrets, and networks → audited actions`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-central-ingress
  namespace: stackrox
spec:
  podSelector:
    matchLabels:
      app: central
  policyTypes: [Ingress]
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              security-access: allowed
```

```bash
oc auth can-i --list --as=system:serviceaccount:stackrox:sensor
oc get networkpolicies,secrets,serviceaccounts -n stackrox
```

**Risks:** bootstrap credential theft, API token compromise, route exposure, weak TLS, support-bundle leakage, and excessive service-account rights. Also verify RHACS and OpenShift compatibility, CR conditions, storage,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 24. Upgrades, Compatibility, Migration, Rollback, and Lifecycle Management

## Q461. How do you plan an RHACS upgrade?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q462. How does the EX430 RHACS 4.6 scope differ from current RHACS releases?

### Answer

Compare the options through lifecycle stage, component ownership, scope, enforcement, availability, and operational cost. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q463. How do RHACS and OpenShift compatibility affect upgrades?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q464. How do you review release notes and support matrices?

### Answer

Treat this as a build, deploy, runtime, vulnerability, network, compliance, identity, availability, and lifecycle decision. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q465. How do OLM channels affect Operator upgrades?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q466. How do you back up before an upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q467. How do you perform pre-upgrade health checks?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q468. How do you upgrade Central through the Operator?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q469. How do secured clusters upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q470. How do you validate Sensor and Collector compatibility?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central. Sensor watches Kubernetes resources on a secured cluster, communicates with Central,...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q471. How do you validate Scanner after upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q472. How do you validate policies and admission control after upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q473. How do you validate integrations after upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q474. How do you handle deprecated features?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q475. How do you perform a canary secured-cluster upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q476. How do you recover from a failed Central upgrade?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q477. How do you roll back when direct downgrade is unsupported?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q478. How do you test upgrades in non-production?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q479. How do you decommission old clusters or integrations?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or secured-cluster component, lifecycle...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml
oc get subscription,csv -n rhacs-operator
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q480. How do you create an enterprise RHACS lifecycle calendar?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **upgrade RHACS while preserving Central and secured-cluster compatibility**. Identify the Central or...

**Flow:** `support matrix, backup, and prechecks → Operator and Central upgrade → secured-cluster rollout → Scanner, policy, integration, and data validation`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get subscription,csv -n rhacs-operator
oc get central,securedcluster -A -o yaml
```

**Risks:** unsupported OpenShift, failed DB migration, mixed versions, deprecated behavior, and unsafe downgrade. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates, Central endpoint,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

# 25. Corporate L3 EX430 Incident and Design Scenarios

## Q481. Central becomes unavailable while secured clusters continue running. How do you respond?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q482. Sensor disconnects from Central across all clusters. How do you investigate?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages policies and integrations, and serves the UI and API. Sensor watches...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q483. Admission control blocks all production deployments. How do you recover?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component,...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q484. A critical policy was accidentally disabled. How do you contain the risk?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q485. A custom runtime policy generates thousands of false positives. How do you stabilize operations?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q486. Registry authentication fails and image scans stop. How do you recover?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q487. A newly disclosed CVE affects hundreds of production deployments. How do you prioritize remediation?

### Answer

Perform this through supported Operator resources and RHACS APIs with audit-first rollout, prechecks, persistence, and rollback. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component,...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q488. A vulnerability exception is approved with an excessive duration. How do you correct it?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. RHACS maps image, node, platform, and supported VM components to vulnerability intelligence and adds deployment context, fixability, exposure, and policy information.

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q489. Collector stops reporting processes and network flows on one node pool. How do you investigate?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. Collector runs with node-level visibility and sends runtime process and network activity to Sensor and Central.

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q490. A generated NetworkPolicy isolates a critical application. How do you recover?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q491. OIDC role mapping grants users excessive access. How do you contain it?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q492. An API token is exposed in CI logs. How do you rotate and investigate?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q493. Object storage is unavailable during a scheduled backup. How do you respond?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q494. A restore completes but secured clusters remain disconnected. How do you recover?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q495. An RHACS upgrade leaves Central DB unavailable. How do you stabilize the platform?

### Answer

Begin by isolating Operator reconciliation, Central and database, Scanner, Sensor, Collector, admission, compliance, registry, identity, notifier, or object-storage dependencies. Central is the management and API plane. It receives secured-cluster data, stores and analyzes security state, manages...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q496. A compliance remediation causes unexpected node reboots. How do you respond?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get events -A --sort-by=.lastTimestamp
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q497. Notifications stop reaching the SIEM during an incident. How do you preserve evidence?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. During an RHACS incident, preserve exact policy revisions, violations, cluster status, component logs, runtime...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q498. How do you lead an RHACS production incident bridge?

### Answer

First contain enforcement, credential, scanning, runtime, or integration impact, preserve RHACS and OpenShift evidence, and recover through a canary secured cluster or policy scope. During an RHACS incident, preserve exact policy revisions, violations, cluster status, component logs, runtime...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q499. What evidence belongs in an RHACS root-cause analysis?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---

## Q500. What should a high-quality EX430 Corporate/L3 runbook contain?

### Answer

Explain this from Central and secured-cluster data collection, analysis, and enforcement perspectives. This topic belongs to the Corporate/L3 responsibility to **lead RHACS incident containment and durable recovery**. Identify the Central or secured-cluster component, lifecycle stage, scope, policy...

**Flow:** `declare scope → pause harmful policy or integration → preserve Central, cluster, runtime, vulnerability, and audit evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm OpenShift and RHACS versions, Operator and CR conditions, component health, scope, credentials, and the recent change.
2. Inspect Central, database, Scanner, Sensor, Collector, admission, compliance, registry, notifier, Events, and logs as applicable.
3. Apply the smallest reversible correction to one cluster, collection, policy, image, or pipeline.
4. Validate scan data, policy results, admission, runtime and network evidence, compliance, notification delivery, and the application.
5. Correct supported CRs, RHACS APIs, Git, CI configuration, monitoring, backup, and the runbook.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rhacs-change-record
  namespace: stackrox
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: component-status-policy-result-and-workload
```

```bash
oc get central,securedcluster -A -o yaml > rhacs-state.yaml
oc get events -A --sort-by=.lastTimestamp
```

**Risks:** fleet-wide deployment outage, lost runtime evidence, credential exposure, broad rollback, and ownerless corrective actions. Also verify RHACS and OpenShift compatibility, CR conditions, storage, certificates,...

**Verify:** Confirm exact versions, Operator and custom-resource conditions, component Pods and logs, secured-cluster status, image or node scan data, policy violation behavior, admission result, runtime and network...

---
