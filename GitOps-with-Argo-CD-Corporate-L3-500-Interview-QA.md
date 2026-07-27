# GitOps with Argo CD - Corporate L3 - 500 Interview Questions and Answers

> Git-compatible edition designed for web rendering.  
> Exactly 500 questions in 25 domains.  
> UTF-8, Unix line endings, balanced Markdown fences, and a simple ASCII filename.

## How to Answer at L3 Level

For each topic, identify the Argo CD resource or component, trace Git retrieval through Kubernetes reconciliation, explain the security and ownership boundaries, show diagnostic commands, and validate the real application outcome.

## Table of Contents

- [01. GitOps Principles, Argo CD Architecture, and Operating Model](#01-gitops-principles-argo-cd-architecture-and-operating-model)
- [02. Installation, Components, Namespaces, and Initial Configuration](#02-installation-components-namespaces-and-initial-configuration)
- [03. Applications, Sources, Destinations, and Reconciliation](#03-applications-sources-destinations-and-reconciliation)
- [04. Sync Semantics, Pruning, Apply Strategies, and Sync Options](#04-sync-semantics-pruning-apply-strategies-and-sync-options)
- [05. Sync Phases, Hooks, Waves, Ordering, and Lifecycle Jobs](#05-sync-phases-hooks-waves-ordering-and-lifecycle-jobs)
- [06. AppProjects, Multi-Tenancy, and Governance Boundaries](#06-appprojects-multi-tenancy-and-governance-boundaries)
- [07. RBAC, Local Accounts, SSO, OIDC, and Identity Integration](#07-rbac-local-accounts-sso-oidc-and-identity-integration)
- [08. Repository Connectivity, Credentials, Certificates, and Git Providers](#08-repository-connectivity-credentials-certificates-and-git-providers)
- [09. Manifest Generation: Directory, Helm, Kustomize, and Jsonnet](#09-manifest-generation-directory-helm-kustomize-and-jsonnet)
- [10. Config Management Plugins and Repository-Server Extensions](#10-config-management-plugins-and-repository-server-extensions)
- [11. Multiple Sources, Dependency Composition, and Repository Design](#11-multiple-sources-dependency-composition-and-repository-design)
- [12. ApplicationSet Architecture and Core Generators](#12-applicationset-architecture-and-core-generators)
- [13. ApplicationSet Lifecycle, Progressive Rollouts, and Multi-Cluster Placement](#13-applicationset-lifecycle-progressive-rollouts-and-multi-cluster-placement)
- [14. Cluster Registration, Credentials, Sharding, and Multi-Cluster Management](#14-cluster-registration-credentials-sharding-and-multi-cluster-management)
- [15. Resource Tracking, Ownership, Diffing, and Shared Resources](#15-resource-tracking-ownership-diffing-and-shared-resources)
- [16. Custom Health Checks, Resource Actions, and Extension Behavior](#16-custom-health-checks-resource-actions-and-extension-behavior)
- [17. Secrets Management, Encryption, and Sensitive Data in GitOps](#17-secrets-management-encryption-and-sensitive-data-in-gitops)
- [18. Security Hardening, Supply Chain, and Policy Enforcement](#18-security-hardening-supply-chain-and-policy-enforcement)
- [19. High Availability, Scalability, Performance, and Capacity](#19-high-availability-scalability-performance-and-capacity)
- [20. Notifications, Webhooks, and Event Integration](#20-notifications-webhooks-and-event-integration)
- [21. CI Integration, Promotion, Image Updates, and Progressive Delivery](#21-ci-integration-promotion-image-updates-and-progressive-delivery)
- [22. Observability, Metrics, Logs, Audit, and Troubleshooting](#22-observability-metrics-logs-audit-and-troubleshooting)
- [23. Backup, Restore, Disaster Recovery, and Upgrades](#23-backup-restore-disaster-recovery-and-upgrades)
- [24. OpenShift, Managed Kubernetes, Network, and Platform Integration](#24-openshift-managed-kubernetes-network-and-platform-integration)
- [25. Corporate L3 Incident, Recovery, and Design Scenarios](#25-corporate-l3-incident-recovery-and-design-scenarios)

---

# 01. GitOps Principles, Argo CD Architecture, and Operating Model

## Q001. Explain GitOps and its core operating principles.

### Answer

**Explain GitOps and its core operating principles.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q002. How does GitOps differ from traditional CI/CD deployment?

### Answer

The correct comparison for **How does GitOps differ from traditional CI/CD deployment** is based on reconciliation ownership, security, failure modes, scale, and auditability. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q003. Why is Git considered the source of truth in GitOps?

### Answer

**Why is Git considered the source of truth in GitOps** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q004. Explain pull-based versus push-based deployment models.

### Answer

The correct comparison for **Explain pull-based versus push-based deployment models.** is based on reconciliation ownership, security, failure modes, scale, and auditability. A pull-based controller inside or connected to the deployment environment observes Git and applies changes, reducing the need to give CI direct cluster credentials.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q005. Explain Argo CD architecture and its core components.

### Answer

**Explain Argo CD architecture and its core components.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD's core components include the API server, repository server, and application controller; common installations also include Redis, Dex, Notifications, and ApplicationSet.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q006. How does the Argo CD application controller reconcile state?

### Answer

**How does the Argo CD application controller reconcile state** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The application controller watches Applications and cluster state, requests rendered manifests, compares desired and live resources, executes operations, and publishes status.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get applications.argoproj.io -A
argocd version
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q007. What are desired state, live state, target state, and sync status?

### Answer

**What are desired state, live state, target state, and sync status** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q008. How do Argo CD and Kubernetes controllers interact?

### Answer

**How do Argo CD and Kubernetes controllers interact** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q009. What is configuration drift and how does Argo CD detect it?

### Answer

**What is configuration drift and how does Argo CD detect it** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q010. How do reconciliation and refresh intervals work?

### Answer

**How do reconciliation and refresh intervals work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q011. What information does Argo CD store in Kubernetes?

### Answer

**What information does Argo CD store in Kubernetes** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q012. Why is Redis considered a disposable cache in Argo CD?

### Answer

**Why is Redis considered a disposable cache in Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Redis as a cache rather than the authoritative database.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q013. How does GitOps improve auditability and change governance?

### Answer

**How does GitOps improve auditability and change governance** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q014. What problems does GitOps not solve automatically?

### Answer

**What problems does GitOps not solve automatically** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q015. How should teams separate CI from continuous delivery?

### Answer

**How should teams separate CI from continuous delivery** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **operate declarative GitOps reconciliation as an enterprise delivery model**.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q016. How do you define GitOps ownership boundaries?

### Answer

**How do you define GitOps ownership boundaries** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get pods
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q017. How do you reduce GitOps blast radius?

### Answer

**How do you reduce GitOps blast radius** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q018. How do you review a workload for GitOps readiness?

### Answer

**How do you review a workload for GitOps readiness** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q019. How do you measure GitOps platform success?

### Answer

**How do you measure GitOps platform success** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
argocd app list
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

## Q020. What are Corporate/L3 Argo CD administrator responsibilities?

### Answer

**What are Corporate/L3 Argo CD administrator responsibilities** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The built-in admin is unrestricted and should be disabled after SSO and tested break-glass access are established.

**Reconciliation flow:** `Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods
kubectl get applications.argoproj.io -A
```

**Key risks:** Typical risks include unclear ownership, manual drift, unreviewed Git changes, false confidence in sync status, and excessive central blast radius. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **Git commit → Argo CD source retrieval/rendering → live-state comparison → Kubernetes apply/prune → controller convergence → status/audit** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs,...

---

# 02. Installation, Components, Namespaces, and Initial Configuration

## Q021. How do you install Argo CD using upstream manifests?

### Answer

**How do you install Argo CD using upstream manifests** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q022. How do namespace-scoped and cluster-wide installation models differ?

### Answer

The correct comparison for **How do namespace-scoped and cluster-wide installation models differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get deploy,statefulset,svc,cm,secret
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q023. What workloads and services are created by a standard Argo CD installation?

### Answer

**What workloads and services are created by a standard Argo CD installation** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q024. How do argocd-server, repo-server, application-controller, Redis, Dex, and ApplicationSet controller differ?

### Answer

The correct comparison for **How do argocd-server, repo-server, application-controller, Redis, Dex, and ApplicationSet controller differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. The application controller watches Applications and cluster state, requests rendered manifests, compares desired and live resources, executes operations, and publishes status.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q025. How do you retrieve and rotate the initial admin password?

### Answer

**How do you retrieve and rotate the initial admin password** should be implemented declaratively through a reviewed, staged, reversible procedure. The built-in admin is unrestricted and should be disabled after SSO and tested break-glass access are established.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q026. How do you expose the Argo CD API server?

### Answer

**How do you expose the Argo CD API server** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get deploy,statefulset,svc,cm,secret
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q027. How do port-forwarding, Ingress, Gateway API, Route, and LoadBalancer exposure differ?

### Answer

The correct comparison for **How do port-forwarding, Ingress, Gateway API, Route, and LoadBalancer exposure differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q028. How do you configure the Argo CD CLI context?

### Answer

**How do you configure the Argo CD CLI context** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get deploy,statefulset,svc,cm,secret
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q029. How do declarative and imperative Argo CD configuration differ?

### Answer

The correct comparison for **How do declarative and imperative Argo CD configuration differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q030. What are argocd-cm, argocd-rbac-cm, argocd-cmd-params-cm, and argocd-secret?

### Answer

**What are argocd-cm, argocd-rbac-cm, argocd-cmd-params-cm, and argocd-secret** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD RBAC maps authenticated users or groups to policy rules over Argo CD resources and actions.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q031. How do command parameters and environment variables configure components?

### Answer

**How do command parameters and environment variables configure components** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q032. How do you install Argo CD in a disconnected environment?

### Answer

**How do you install Argo CD in a disconnected environment** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset,svc,cm,secret
argocd admin initial-password -n argocd
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q033. How do you customize installation manifests safely?

### Answer

**How do you customize installation manifests safely** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset,svc,cm,secret
argocd admin initial-password -n argocd
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q034. How do Helm charts and Operators install Argo CD?

### Answer

**How do Helm charts and Operators install Argo CD** should be implemented declaratively through a reviewed, staged, reversible procedure. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q035. How do you validate an Argo CD installation?

### Answer

**How do you validate an Argo CD installation** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset,svc,cm,secret
argocd admin initial-password -n argocd
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q036. How do you troubleshoot an Argo CD component that is not ready?

### Answer

For **How do you troubleshoot an Argo CD component that is not ready**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q037. How do you handle Pod Security or SCC restrictions during installation?

### Answer

**How do you handle Pod Security or SCC restrictions during installation** should be implemented declaratively through a reviewed, staged, reversible procedure. Argo CD should use least privilege, protected Git, verified artifacts, restricted egress, network policy, SSO, audit logging, and tightly governed plugins.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get deploy,statefulset,svc,cm,secret
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q038. How do you define resource requests and limits for Argo CD components?

### Answer

**How do you define resource requests and limits for Argo CD components** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset,svc,cm,secret
argocd admin initial-password -n argocd
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q039. How do you create a repeatable production installation workflow?

### Answer

**How do you create a repeatable production installation workflow** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin initial-password -n argocd
argocd login <server>
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q040. How do you decide between one central instance and multiple Argo CD instances?

### Answer

**How do you decide between one central instance and multiple Argo CD instances** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **install and configure Argo CD reproducibly**.

**Reconciliation flow:** `installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd login <server>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include insecure initial admin, unsupported customization, missing resources, ingress/gRPC issues, and undersized components. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **installation manifests/chart/operator → CRDs/config/Secrets → components/services → exposure/CLI → validation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 03. Applications, Sources, Destinations, and Reconciliation

## Q041. Explain the Argo CD Application custom resource.

### Answer

**Explain the Argo CD Application custom resource.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q042. How do source, destination, project, and syncPolicy fields work?

### Answer

**How do source, destination, project, and syncPolicy fields work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app history <app>
argocd app sync <app> --preview-changes
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q043. How do targetRevision, path, and chart fields select desired state?

### Answer

**How do targetRevision, path, and chart fields select desired state** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app terminate-op <app>
argocd app get <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q044. How do you create an Application declaratively?

### Answer

**How do you create an Application declaratively** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app history <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q045. How do you create and inspect an Application with the CLI?

### Answer

**How do you create and inspect an Application with the CLI** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q046. How do automated sync, prune, and selfHeal work?

### Answer

**How do automated sync, prune, and selfHeal work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Automated sync can apply drift, while `prune` removes resources absent from desired state and `selfHeal` corrects live changes.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q047. How do allowEmpty and automated sync controls work?

### Answer

**How do allowEmpty and automated sync controls work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Automated sync can apply drift, while `prune` removes resources absent from desired state and `selfHeal` corrects live changes.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app history <app>
argocd app sync <app> --preview-changes
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q048. How do manual refresh and hard refresh differ?

### Answer

The correct comparison for **How do manual refresh and hard refresh differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app history <app>
argocd app sync <app> --preview-changes
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q049. How do you perform a normal, forced, or selective sync?

### Answer

**How do you perform a normal, forced, or selective sync** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app terminate-op <app>
argocd app get <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q050. How does Argo CD determine application health?

### Answer

**How does Argo CD determine application health** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app terminate-op <app>
argocd app get <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q051. How do health status and sync status differ?

### Answer

The correct comparison for **How do health status and sync status differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. Application health aggregates resource health.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app history <app>
argocd app sync <app> --preview-changes
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q052. How do application history and rollback work?

### Answer

**How do application history and rollback work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q053. How do you terminate an in-progress operation?

### Answer

**How do you terminate an in-progress operation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q054. How do you suspend or disable application reconciliation?

### Answer

**How do you suspend or disable application reconciliation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app history <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q055. How do you rename or migrate an Application safely?

### Answer

**How do you rename or migrate an Application safely** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app history <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q056. How do application finalizers affect deletion?

### Answer

**How do application finalizers affect deletion** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app history <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q057. How do you prevent accidental cascading deletion?

### Answer

**How do you prevent accidental cascading deletion** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage Application lifecycle and reconciliation safely**.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app terminate-op <app>
argocd app get <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q058. How do you troubleshoot an Application stuck Progressing?

### Answer

For **How do you troubleshoot an Application stuck Progressing**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app history <app>
argocd app sync <app> --preview-changes
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q059. How do you troubleshoot an Application stuck Unknown?

### Answer

For **How do you troubleshoot an Application stuck Unknown**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --preview-changes
argocd app terminate-op <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q060. How do you define an enterprise Application onboarding standard?

### Answer

**How do you define an enterprise Application onboarding standard** is a platform governance and trust-boundary decision, not only an Application YAML choice. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app terminate-op <app>
argocd app get <app>
```

**Key risks:** Typical risks include cascading deletion, stale revisions, stuck operations, weak health checks, and unmanaged Application creation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **Application source/destination/project → refresh/render → compare → sync operation → health/history/finalizer** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 04. Sync Semantics, Pruning, Apply Strategies, and Sync Options

## Q061. Explain the complete Argo CD synchronization workflow.

### Answer

**Explain the complete Argo CD synchronization workflow.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
argocd app sync <app> --dry-run
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q062. How do client-side apply and server-side apply differ in Argo CD?

### Answer

The correct comparison for **How do client-side apply and server-side apply differ in Argo CD** is based on reconciliation ownership, security, failure modes, scale, and auditability. Server-side apply lets the API server track field managers and can handle large resources, but ownership conflicts and migration from client-side apply require planning.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q063. How does Replace differ from Apply?

### Answer

The correct comparison for **How does Replace differ from Apply** is based on reconciliation ownership, security, failure modes, scale, and auditability. Replace uses create/replace semantics rather than merge-based apply and can recreate resources.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q064. How does Force sync affect resources?

### Answer

**How does Force sync affect resources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q065. How do prune propagation policies work?

### Answer

**How do prune propagation policies work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Pruning deletes tracked live resources no longer in desired state.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
argocd app sync <app> --dry-run
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q066. How does PruneLast change deletion ordering?

### Answer

**How does PruneLast change deletion ordering** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Pruning deletes tracked live resources no longer in desired state.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q067. How does SkipDryRunOnMissingResource work?

### Answer

**How does SkipDryRunOnMissingResource work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o yaml
argocd app diff <app>
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q068. How does Validate=false affect synchronization?

### Answer

**How does Validate=false affect synchronization** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q069. How does CreateNamespace work?

### Answer

**How does CreateNamespace work** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q070. How do managedNamespaceMetadata labels and annotations work?

### Answer

**How do managedNamespaceMetadata labels and annotations work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
argocd app sync <app> --dry-run
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q071. How does ApplyOutOfSyncOnly reduce API operations?

### Answer

**How does ApplyOutOfSyncOnly reduce API operations** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q072. How do RespectIgnoreDifferences and ignoreDifferences interact?

### Answer

The correct comparison for **How do RespectIgnoreDifferences and ignoreDifferences interact** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q073. How does FailOnSharedResource protect resource ownership?

### Answer

**How does FailOnSharedResource protect resource ownership** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q074. How does Prune=false or Delete=false protect resources?

### Answer

**How does Prune=false or Delete=false protect resources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Pruning deletes tracked live resources no longer in desired state.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q075. How do comparison options differ from sync options?

### Answer

The correct comparison for **How do comparison options differ from sync options** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q076. How do you choose a safe apply strategy for large CRDs?

### Answer

**How do you choose a safe apply strategy for large CRDs** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --dry-run
argocd app sync <app> --prune
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q077. How do immutable-field changes affect synchronization?

### Answer

**How do immutable-field changes affect synchronization** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o yaml
argocd app diff <app>
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q078. How do you troubleshoot a sync that repeatedly fails during apply?

### Answer

For **How do you troubleshoot a sync that repeatedly fails during apply**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
argocd app sync <app> --dry-run
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q079. How do you validate destructive changes before enabling prune?

### Answer

**How do you validate destructive changes before enabling prune** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Pruning deletes tracked live resources no longer in desired state.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o yaml
argocd app diff <app>
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q080. How do you create corporate sync-option standards?

### Answer

**How do you create corporate sync-option standards** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **choose safe synchronization and pruning semantics**.

**Reconciliation flow:** `comparison → dry-run/validation → apply/replace/force → prune → health and operation status`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app sync <app> --prune
kubectl -n argocd get application <app> -o yaml
```

**Key risks:** Typical risks include destructive prune, immutable-field failure, field-ownership conflict, skipped validation, and unsafe Force/Replace. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **comparison → dry-run/validation → apply/replace/force → prune → health and operation status** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 05. Sync Phases, Hooks, Waves, Ordering, and Lifecycle Jobs

## Q081. Explain PreSync, Sync, PostSync, SyncFail, and PostDelete phases.

### Answer

**Explain PreSync, Sync, PostSync, SyncFail, and PostDelete phases.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q082. How do Argo CD resource hooks work?

### Answer

**How do Argo CD resource hooks work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q083. How do sync waves control ordering?

### Answer

**How do sync waves control ordering** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Sync waves order resources numerically within phases; Argo CD waits for health before advancing, so one unhealthy resource can block later waves.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q084. How are phase, wave, kind, and name used to determine ordering?

### Answer

**How are phase, wave, kind, and name used to determine ordering** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q085. How do you use negative sync waves?

### Answer

**How do you use negative sync waves** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Sync waves order resources numerically within phases; Argo CD waits for health before advancing, so one unhealthy resource can block later waves.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q086. How do hooks differ from ordinary managed resources?

### Answer

The correct comparison for **How do hooks differ from ordinary managed resources** is based on reconciliation ownership, security, failure modes, scale, and auditability. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q087. How do hook deletion policies work?

### Answer

**How do hook deletion policies work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q088. How do BeforeHookCreation, HookSucceeded, and HookFailed differ?

### Answer

The correct comparison for **How do BeforeHookCreation, HookSucceeded, and HookFailed differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q089. How do you run database migrations safely with hooks?

### Answer

**How do you run database migrations safely with hooks** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q090. How do you implement pre-deployment validation?

### Answer

**How do you implement pre-deployment validation** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q091. How do you implement post-deployment smoke tests?

### Answer

**How do you implement post-deployment smoke tests** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q092. How do you implement cleanup after failed synchronization?

### Answer

**How do you implement cleanup after failed synchronization** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q093. How do you avoid non-idempotent hook behavior?

### Answer

**How do you avoid non-idempotent hook behavior** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q094. How do Jobs and TTL controllers affect hook history?

### Answer

**How do Jobs and TTL controllers affect hook history** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q095. How do you troubleshoot a hook stuck Running?

### Answer

For **How do you troubleshoot a hook stuck Running**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get events -n <namespace> --sort-by=.lastTimestamp
argocd app logs <app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q096. How do you troubleshoot a sync wave that never advances?

### Answer

For **How do you troubleshoot a sync wave that never advances**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Sync waves order resources numerically within phases; Argo CD waits for health before advancing, so one unhealthy resource can block later waves.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q097. How do unhealthy resources block later waves?

### Answer

**How do unhealthy resources block later waves** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q098. How do you coordinate CRDs and custom resources?

### Answer

**How do you coordinate CRDs and custom resources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q099. How do you coordinate Operators and operand resources?

### Answer

**How do you coordinate Operators and operand resources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **coordinate lifecycle operations through phases and waves**.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl get jobs -A -l argocd.argoproj.io/instance=<app>
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q100. How do you design a production lifecycle-hook framework?

### Answer

**How do you design a production lifecycle-hook framework** is a platform governance and trust-boundary decision, not only an Application YAML choice. Hooks are annotated resources executed in lifecycle phases.

**Reconciliation flow:** `phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app logs <app>
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include non-idempotent migrations, stuck Jobs, missing deletion policy, unhealthy blockers, and ordering assumptions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **phase/wave ordering → hook/resource execution → health gate → subsequent wave → cleanup** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 06. AppProjects, Multi-Tenancy, and Governance Boundaries

## Q101. Explain the purpose of an Argo CD AppProject.

### Answer

**Explain the purpose of an Argo CD AppProject.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj role list <project>
argocd proj get <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q102. How do sourceRepos and destinations restrict Applications?

### Answer

**How do sourceRepos and destinations restrict Applications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj get <project>
argocd proj list
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q103. How do sourceNamespaces affect Application placement?

### Answer

**How do sourceNamespaces affect Application placement** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj get <project>
argocd proj list
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q104. How do clusterResourceWhitelist and blacklist rules work?

### Answer

**How do clusterResourceWhitelist and blacklist rules work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q105. How do namespaceResourceWhitelist and blacklist rules work?

### Answer

**How do namespaceResourceWhitelist and blacklist rules work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q106. How do orphaned-resource monitoring settings work?

### Answer

**How do orphaned-resource monitoring settings work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q107. How do project roles work?

### Answer

**How do project roles work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q108. How do project role tokens work?

### Answer

**How do project role tokens work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj role list <project>
argocd proj get <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q109. How do AppProject sync windows work?

### Answer

**How do AppProject sync windows work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q110. How do allow and deny sync windows interact?

### Answer

**How do allow and deny sync windows interact** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q111. How do you prevent teams from deploying cluster-scoped resources?

### Answer

**How do you prevent teams from deploying cluster-scoped resources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q112. How do you restrict a team to approved clusters and namespaces?

### Answer

**How do you restrict a team to approved clusters and namespaces** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj get <project>
argocd proj list
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q113. How do you restrict source repositories?

### Answer

**How do you restrict source repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q114. How do you prevent an Application from moving to the default project?

### Answer

**How do you prevent an Application from moving to the default project** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj get <project>
argocd proj list
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q115. Why is the default project risky without restrictions?

### Answer

**Why is the default project risky without restrictions** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q116. How do you design project-per-team versus project-per-environment?

### Answer

**How do you design project-per-team versus project-per-environment** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **enforce multi-tenant governance through AppProjects**.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q117. How do you implement namespace tenancy with AppProjects?

### Answer

**How do you implement namespace tenancy with AppProjects** should be implemented declaratively through a reviewed, staged, reversible procedure. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q118. How do you audit AppProject changes?

### Answer

**How do you audit AppProject changes** should be explained from both Argo CD controller mechanics and enterprise delivery operations. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q119. How do you troubleshoot an Application rejected by its project?

### Answer

For **How do you troubleshoot an Application rejected by its project**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd proj list
kubectl -n argocd get appproject <project> -o yaml
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q120. How do you create an enterprise AppProject baseline?

### Answer

**How do you create an enterprise AppProject baseline** is a platform governance and trust-boundary decision, not only an Application YAML choice. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get appproject <project> -o yaml
argocd proj role list <project>
```

**Key risks:** Typical risks include default-project overreach, unrestricted cluster resources, broad repositories, weak tenant isolation, and accidental project deletion. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination...

**Verification:** Validate **Application identity → project source/destination/resource checks → sync window/RBAC → allowed reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 07. RBAC, Local Accounts, SSO, OIDC, and Identity Integration

## Q121. Explain the Argo CD RBAC model.

### Answer

**Explain the Argo CD RBAC model.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD RBAC maps authenticated users or groups to policy rules over Argo CD resources and actions.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account list
kubectl -n argocd get cm argocd-rbac-cm -o yaml
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q122. How do policy.csv and policy.default work?

### Answer

**How do policy.csv and policy.default work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q123. How do roles, groups, subjects, resources, actions, and objects form RBAC rules?

### Answer

**How do roles, groups, subjects, resources, actions, and objects form RBAC rules** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD RBAC maps authenticated users or groups to policy rules over Argo CD resources and actions.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q124. How does glob matching differ from regex matching in RBAC?

### Answer

The correct comparison for **How does glob matching differ from regex matching in RBAC** is based on reconciliation ownership, security, failure modes, scale, and auditability. Argo CD RBAC maps authenticated users or groups to policy rules over Argo CD resources and actions.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q125. How do deny rules interact with allow rules?

### Answer

**How do deny rules interact with allow rules** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q126. How do you map OIDC groups to Argo CD roles?

### Answer

**How do you map OIDC groups to Argo CD roles** should be explained from both Argo CD controller mechanics and enterprise delivery operations. OIDC login depends on issuer discovery, client configuration, TLS trust, redirect URI, token signing keys, and group claims used by RBAC.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-rbac-cm -o yaml
argocd admin settings rbac can <subject> get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q127. How do local accounts work?

### Answer

**How do local accounts work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q128. How do you disable the built-in admin account?

### Answer

**How do you disable the built-in admin account** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The built-in admin is unrestricted and should be disabled after SSO and tested break-glass access are established.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account list
kubectl -n argocd get cm argocd-rbac-cm -o yaml
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q129. How do you generate and revoke Argo CD API tokens?

### Answer

**How do you generate and revoke Argo CD API tokens** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q130. How does Dex integrate with identity providers?

### Answer

**How does Dex integrate with identity providers** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q131. How do direct OIDC and Dex-based SSO differ?

### Answer

The correct comparison for **How do direct OIDC and Dex-based SSO differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. OIDC login depends on issuer discovery, client configuration, TLS trust, redirect URI, token signing keys, and group claims used by RBAC.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account list
kubectl -n argocd get cm argocd-rbac-cm -o yaml
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q132. How do you integrate Argo CD with Keycloak?

### Answer

**How do you integrate Argo CD with Keycloak** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q133. How do you integrate Argo CD with LDAP through an identity provider?

### Answer

**How do you integrate Argo CD with LDAP through an identity provider** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q134. How do you configure SAML-backed SSO through Dex?

### Answer

**How do you configure SAML-backed SSO through Dex** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q135. How do you troubleshoot an SSO redirect loop?

### Answer

For **How do you troubleshoot an SSO redirect loop**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account list
kubectl -n argocd get cm argocd-rbac-cm -o yaml
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q136. How do you troubleshoot missing group claims?

### Answer

For **How do you troubleshoot missing group claims**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q137. How do you troubleshoot a user who can log in but cannot access Applications?

### Answer

For **How do you troubleshoot a user who can log in but cannot access Applications**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q138. How do you design break-glass access?

### Answer

**How do you design break-glass access** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd account can-i get applications '<project>/*'
argocd account list
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q139. How do you conduct periodic Argo CD access reviews?

### Answer

**How do you conduct periodic Argo CD access reviews** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin settings rbac can <subject> get applications '<project>/*'
argocd account can-i get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q140. How do you implement least-privilege identity governance?

### Answer

**How do you implement least-privilege identity governance** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **provide least-privilege Argo CD access with SSO and break-glass controls**.

**Reconciliation flow:** `authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-rbac-cm -o yaml
argocd admin settings rbac can <subject> get applications '<project>/*'
```

**Key risks:** Typical risks include admin account exposure, missing claims, broad default policy, stale tokens, and SSO-only lockout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **authentication/local token → group/subject claims → RBAC policy evaluation → API action → Kubernetes operation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 08. Repository Connectivity, Credentials, Certificates, and Git Providers

## Q141. How does repo-server access Git repositories?

### Answer

**How does repo-server access Git repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo get <repo-url>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q142. How do HTTPS repository credentials work?

### Answer

**How do HTTPS repository credentials work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo get <repo-url>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q143. How do SSH repository credentials work?

### Answer

**How do SSH repository credentials work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q144. How do credential templates work?

### Answer

**How do credential templates work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
argocd repo get <repo-url>
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q145. How do repository Secrets work in declarative configuration?

### Answer

**How do repository Secrets work in declarative configuration** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q146. How do you configure GitHub App credentials?

### Answer

**How do you configure GitHub App credentials** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo get <repo-url>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q147. How do you configure GitLab or Bitbucket authentication?

### Answer

**How do you configure GitLab or Bitbucket authentication** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q148. How do you manage private Helm repositories?

### Answer

**How do you manage private Helm repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
argocd repo get <repo-url>
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q149. How do you manage OCI Helm repositories?

### Answer

**How do you manage OCI Helm repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
argocd repo list
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q150. How do you configure custom CA certificates for repositories?

### Answer

**How do you configure custom CA certificates for repositories** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
argocd repo list
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q151. How do you manage SSH known_hosts safely?

### Answer

**How do you manage SSH known_hosts safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. SSH host verification protects repository connections from redirection attacks.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
argocd repo list
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q152. How do you rotate repository credentials without interruption?

### Answer

**How do you rotate repository credentials without interruption** should be implemented declaratively through a reviewed, staged, reversible procedure. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo get <repo-url>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q153. How do you restrict repositories by AppProject?

### Answer

**How do you restrict repositories by AppProject** should be explained from both Argo CD controller mechanics and enterprise delivery operations. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q154. How do proxies affect repository access?

### Answer

**How do proxies affect repository access** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q155. How do you troubleshoot repository authentication failure?

### Answer

For **How do you troubleshoot repository authentication failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
argocd repo list
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q156. How do you troubleshoot TLS unknown-authority errors?

### Answer

For **How do you troubleshoot TLS unknown-authority errors**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q157. How do you troubleshoot SSH host-key verification failure?

### Answer

For **How do you troubleshoot SSH host-key verification failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **connect to trusted Git and Helm sources securely**.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
argocd repo get <repo-url>
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q158. How do you troubleshoot repository timeouts or large-repository performance?

### Answer

For **How do you troubleshoot repository timeouts or large-repository performance**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q159. How do you protect repository credentials from application teams?

### Answer

**How do you protect repository credentials from application teams** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
argocd repo get <repo-url>
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q160. How do you design repository-access governance at enterprise scale?

### Answer

**How do you design repository-access governance at enterprise scale** is a platform governance and trust-boundary decision, not only an Application YAML choice. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo get <repo-url>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=repository
```

**Key risks:** Typical risks include expired credentials, untrusted CAs, SSH host-key failures, broad credential reuse, and slow monorepos. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo Secret/credential template → TLS/SSH/proxy authentication → clone/fetch → revision checkout → manifest generation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 09. Manifest Generation: Directory, Helm, Kustomize, and Jsonnet

## Q161. How does Argo CD detect the manifest-generation tool?

### Answer

**How does Argo CD detect the manifest-generation tool** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q162. How does a plain directory source work?

### Answer

**How does a plain directory source work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl apply --dry-run=server -f rendered.yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q163. How do include and exclude patterns work for directory sources?

### Answer

**How do include and exclude patterns work for directory sources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q164. How does Argo CD render Helm charts?

### Answer

**How does Argo CD render Helm charts** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
helm template <release> <chart> -f values.yaml
kustomize build overlays/prod
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q165. How do valueFiles, values, valuesObject, and parameters differ?

### Answer

The correct comparison for **How do valueFiles, values, valuesObject, and parameters differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
helm template <release> <chart> -f values.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q166. How does Helm value precedence work in Argo CD?

### Answer

**How does Helm value precedence work in Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q167. How do Helm releaseName and namespace affect rendering?

### Answer

**How do Helm releaseName and namespace affect rendering** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q168. How do you pass Helm files from multiple sources?

### Answer

**How do you pass Helm files from multiple sources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Multiple sources render independently and combine resources.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl apply --dry-run=server -f rendered.yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q169. How does Argo CD render Kustomize applications?

### Answer

**How does Argo CD render Kustomize applications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q170. How do namePrefix, nameSuffix, commonLabels, images, and patches work?

### Answer

**How do namePrefix, nameSuffix, commonLabels, images, and patches work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q171. How do Kustomize versions affect rendering?

### Answer

**How do Kustomize versions affect rendering** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD runs a bundled or configured Kustomize version.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
helm template <release> <chart> -f values.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q172. How does Jsonnet integration work?

### Answer

**How does Jsonnet integration work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
helm template <release> <chart> -f values.yaml
kustomize build overlays/prod
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q173. How do Jsonnet extVars and TLAs work?

### Answer

**How do Jsonnet extVars and TLAs work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
helm template <release> <chart> -f values.yaml
kustomize build overlays/prod
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q174. How do environment variables enter manifest generation?

### Answer

**How do environment variables enter manifest generation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q175. How do you render and validate manifests outside Argo CD?

### Answer

**How do you render and validate manifests outside Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q176. How do you troubleshoot Helm template errors?

### Answer

For **How do you troubleshoot Helm template errors**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Argo CD uses Helm primarily as a template renderer; Helm release state is not the normal ownership record.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
helm template <release> <chart> -f values.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q177. How do you troubleshoot Kustomize build failures?

### Answer

For **How do you troubleshoot Kustomize build failures**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Argo CD runs a bundled or configured Kustomize version.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
helm template <release> <chart> -f values.yaml
kustomize build overlays/prod
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q178. How do you prevent non-reproducible remote dependencies?

### Answer

**How do you prevent non-reproducible remote dependencies** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate reproducible manifests from supported tools**.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q179. How do you pin chart and Kustomize behavior?

### Answer

**How do you pin chart and Kustomize behavior** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD runs a bundled or configured Kustomize version.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
helm template <release> <chart> -f values.yaml
kustomize build overlays/prod
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q180. How do you choose between Helm, Kustomize, and plain manifests?

### Answer

**How do you choose between Helm, Kustomize, and plain manifests** is a platform governance and trust-boundary decision, not only an Application YAML choice. Argo CD runs a bundled or configured Kustomize version.

**Reconciliation flow:** `source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kustomize build overlays/prod
kubectl apply --dry-run=server -f rendered.yaml
```

**Key risks:** Typical risks include un-pinned dependencies, hidden Helm precedence, version drift, remote bases, and environment-specific rendering. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **source detection → Helm/Kustomize/Jsonnet/directory rendering → normalized YAML → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 10. Config Management Plugins and Repository-Server Extensions

## Q181. What is an Argo CD config management plugin?

### Answer

**What is an Argo CD config management plugin** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q182. How do sidecar-based config management plugins work?

### Answer

**How do sidecar-based config management plugins work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q183. How do discover, init, generate, and parameters commands work?

### Answer

**How do discover, init, generate, and parameters commands work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **extend manifest generation without compromising repo-server**.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q184. How do you register a plugin declaratively?

### Answer

**How do you register a plugin declaratively** should be implemented declaratively through a reviewed, staged, reversible procedure. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q185. How does repo-server communicate with a plugin sidecar?

### Answer

**How does repo-server communicate with a plugin sidecar** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q186. How do you package plugin binaries and dependencies?

### Answer

**How do you package plugin binaries and dependencies** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
argocd app manifests <app>
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q187. How do you protect plugin execution from untrusted repositories?

### Answer

**How do you protect plugin execution from untrusted repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q188. How do you restrict environment variables available to plugins?

### Answer

**How do you restrict environment variables available to plugins** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q189. How do timeouts affect plugin execution?

### Answer

**How do timeouts affect plugin execution** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q190. How do you troubleshoot a plugin-not-found error?

### Answer

For **How do you troubleshoot a plugin-not-found error**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q191. How do you troubleshoot plugin output that is not valid YAML?

### Answer

For **How do you troubleshoot plugin output that is not valid YAML**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q192. How do you troubleshoot a plugin sidecar socket failure?

### Answer

For **How do you troubleshoot a plugin sidecar socket failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
argocd app manifests <app>
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q193. How do you handle multiple plugin versions?

### Answer

**How do you handle multiple plugin versions** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q194. How do you scale resource-intensive plugins?

### Answer

**How do you scale resource-intensive plugins** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q195. How do you cache plugin dependencies safely?

### Answer

**How do you cache plugin dependencies safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q196. How do you validate plugin output in CI?

### Answer

**How do you validate plugin output in CI** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q197. How do you manage secrets required during manifest generation?

### Answer

**How do you manage secrets required during manifest generation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **extend manifest generation without compromising repo-server**.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q198. Why should secret decryption in repo-server be carefully designed?

### Answer

**Why should secret decryption in repo-server be carefully designed** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **extend manifest generation without compromising repo-server**.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
argocd app manifests <app>
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q199. How do you upgrade a plugin without breaking Applications?

### Answer

**How do you upgrade a plugin without breaking Applications** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server -c <plugin-sidecar> --since=30m
argocd app manifests <app>
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q200. How do you define an enterprise plugin-governance process?

### Answer

**How do you define an enterprise plugin-governance process** is a platform governance and trust-boundary decision, not only an Application YAML choice. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
```

**Key risks:** Typical risks include arbitrary code execution, secret exfiltration, invalid output, socket/timeouts, and versioned plugin drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **repo-server → plugin sidecar discovery/init/generate → YAML stream → comparison** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 11. Multiple Sources, Dependency Composition, and Repository Design

## Q201. How do multiple sources work in an Argo CD Application?

### Answer

**How do multiple sources work in an Argo CD Application** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Multiple sources render independently and combine resources.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> -o yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q202. How does the ref field allow one source to reference another?

### Answer

**How does the ref field allow one source to reference another** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q203. How do you combine a Helm chart with values from a Git repository?

### Answer

**How do you combine a Helm chart with values from a Git repository** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q204. How does resource override behavior work across multiple sources?

### Answer

**How does resource override behavior work across multiple sources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Multiple sources render independently and combine resources.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q205. What are RepeatedResourceWarning messages?

### Answer

**What are RepeatedResourceWarning messages** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q206. When are multiple sources appropriate?

### Answer

**When are multiple sources appropriate** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Multiple sources render independently and combine resources.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q207. When should ApplicationSet or an umbrella repository be preferred?

### Answer

**When should ApplicationSet or an umbrella repository be preferred** should be explained from both Argo CD controller mechanics and enterprise delivery operations. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q208. How do you troubleshoot a missing value-file reference?

### Answer

For **How do you troubleshoot a missing value-file reference**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q209. How do you pin revisions across multiple sources?

### Answer

**How do you pin revisions across multiple sources** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Multiple sources render independently and combine resources.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q210. How do you coordinate dependency changes across repositories?

### Answer

**How do you coordinate dependency changes across repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> -o yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q211. How do monorepo and polyrepo strategies differ?

### Answer

The correct comparison for **How do monorepo and polyrepo strategies differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> -o yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q212. How do repository-per-application and repository-per-environment differ?

### Answer

The correct comparison for **How do repository-per-application and repository-per-environment differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q213. How do you prevent a large monorepo from overloading repo-server?

### Answer

**How do you prevent a large monorepo from overloading repo-server** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q214. How do you organize platform and workload configuration?

### Answer

**How do you organize platform and workload configuration** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q215. How do you separate reusable bases from environment overlays?

### Answer

**How do you separate reusable bases from environment overlays** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q216. How do you manage cross-repository promotion?

### Answer

**How do you manage cross-repository promotion** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q217. How do you preserve atomicity across multiple repositories?

### Answer

**How do you preserve atomicity across multiple repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> -o yaml
argocd app manifests <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q218. How do you audit which repository produced a live resource?

### Answer

**How do you audit which repository produced a live resource** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q219. How do you detect stale or abandoned configuration repositories?

### Answer

**How do you detect stale or abandoned configuration repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **compose controlled dependencies across repositories**.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get application <app> -o jsonpath='{.spec.sources}'
argocd app get <app> -o yaml
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q220. How do you create an enterprise repository-layout standard?

### Answer

**How do you create an enterprise repository-layout standard** is a platform governance and trust-boundary decision, not only an Application YAML choice. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app manifests <app>
argocd app diff <app>
```

**Key risks:** Typical risks include non-atomic repositories, repeated resources, stale references, dependency races, and unclear provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **multiple revisions/sources → ref resolution → independent rendering → resource combination → duplicate detection** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 12. ApplicationSet Architecture and Core Generators

## Q221. Explain the ApplicationSet controller and ApplicationSet resource.

### Answer

**Explain the ApplicationSet controller and ApplicationSet resource.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
argocd app list
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q222. How does the list generator work?

### Answer

**How does the list generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q223. How does the clusters generator work?

### Answer

**How does the clusters generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q224. How does the Git directory generator work?

### Answer

**How does the Git directory generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
argocd app list
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q225. How does the Git file generator work?

### Answer

**How does the Git file generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q226. How does the SCM provider generator work?

### Answer

**How does the SCM provider generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q227. How does the cluster decision resource generator work?

### Answer

**How does the cluster decision resource generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q228. How does the pull-request generator work?

### Answer

**How does the pull-request generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q229. How does the plugin generator work?

### Answer

**How does the plugin generator work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q230. How does the matrix generator combine parameters?

### Answer

**How does the matrix generator combine parameters** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q231. How does the merge generator combine parameters?

### Answer

**How does the merge generator combine parameters** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q232. How do Go templates work in ApplicationSet?

### Answer

**How do Go templates work in ApplicationSet** should be explained from both Argo CD controller mechanics and enterprise delivery operations. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q233. How do missingkey options affect templates?

### Answer

**How do missingkey options affect templates** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q234. How do templatePatch and conditional templating work?

### Answer

**How do templatePatch and conditional templating work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q235. How are generated Application names made unique?

### Answer

**How are generated Application names made unique** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q236. How does requeueAfterSeconds affect generator refresh?

### Answer

**How does requeueAfterSeconds affect generator refresh** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q237. How do you preview generated Applications?

### Answer

**How do you preview generated Applications** is a platform governance and trust-boundary decision, not only an Application YAML choice. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q238. How do you troubleshoot an ApplicationSet that generates no Applications?

### Answer

For **How do you troubleshoot an ApplicationSet that generates no Applications**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q239. How do you troubleshoot duplicate generated names?

### Answer

For **How do you troubleshoot duplicate generated names**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications -l argocd.argoproj.io/application-set-name=<name>
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q240. How do you select the right generator for an enterprise use case?

### Answer

**How do you select the right generator for an enterprise use case** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **generate Applications at scale**.

**Reconciliation flow:** `ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
argocd app list
```

**Key risks:** Typical risks include empty generation, duplicate names, broad SCM discovery, malformed parameters, and uncontrolled fan-out. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **ApplicationSet generator data → Go template/templatePatch → Application objects → Argo CD reconciliation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 13. ApplicationSet Lifecycle, Progressive Rollouts, and Multi-Cluster Placement

## Q241. How does ApplicationSet create, update, and delete Applications?

### Answer

**How does ApplicationSet create, update, and delete Applications** should be implemented declaratively through a reviewed, staged, reversible procedure. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q242. How do applicationsSync policies control generated Applications?

### Answer

**How do applicationsSync policies control generated Applications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q243. How do create-only, create-update, and create-delete behaviors differ?

### Answer

The correct comparison for **How do create-only, create-update, and create-delete behaviors differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q244. How does preserveResourcesOnDeletion work?

### Answer

**How does preserveResourcesOnDeletion work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q245. How do owner references and finalizers affect generated Applications?

### Answer

**How do owner references and finalizers affect generated Applications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q246. How do you prevent accidental deletion from a generator change?

### Answer

**How do you prevent accidental deletion from a generator change** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q247. How do RollingSync strategies work?

### Answer

**How do RollingSync strategies work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q248. How do matchExpressions select rollout groups?

### Answer

**How do matchExpressions select rollout groups** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q249. How does maxUpdate control progressive synchronization?

### Answer

**How does maxUpdate control progressive synchronization** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications --show-labels
argocd app get <generated-app>
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q250. How do you pause an ApplicationSet rollout?

### Answer

**How do you pause an ApplicationSet rollout** should be explained from both Argo CD controller mechanics and enterprise delivery operations. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q251. How do you implement dev-test-production waves with ApplicationSet?

### Answer

**How do you implement dev-test-production waves with ApplicationSet** should be implemented declaratively through a reviewed, staged, reversible procedure. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications --show-labels
argocd app get <generated-app>
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q252. How do you deploy an application to every registered cluster?

### Answer

**How do you deploy an application to every registered cluster** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q253. How do cluster labels drive placement?

### Answer

**How do cluster labels drive placement** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q254. How do you exclude clusters from generation?

### Answer

**How do you exclude clusters from generation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q255. How do you handle cluster decommissioning safely?

### Answer

**How do you handle cluster decommissioning safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications --show-labels
argocd app get <generated-app>
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q256. How do you manage region- or tenant-specific parameters?

### Answer

**How do you manage region- or tenant-specific parameters** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q257. How do you test generator changes before merge?

### Answer

**How do you test generator changes before merge** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **control generated-Application lifecycle and progressive placement**.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <generated-app>
kubectl -n argocd get events --sort-by=.lastTimestamp
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q258. How do you troubleshoot generated Applications that remain OutOfSync?

### Answer

For **How do you troubleshoot generated Applications that remain OutOfSync**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications --show-labels
argocd app get <generated-app>
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q259. How do you migrate hand-created Applications into ApplicationSet?

### Answer

**How do you migrate hand-created Applications into ApplicationSet** should be implemented declaratively through a reviewed, staged, reversible procedure. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applicationset <name> -o yaml
kubectl -n argocd get applications --show-labels
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q260. How do you create an enterprise multi-cluster ApplicationSet standard?

### Answer

**How do you create an enterprise multi-cluster ApplicationSet standard** is a platform governance and trust-boundary decision, not only an Application YAML choice. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd get applicationset <name> -o yaml
```

**Key risks:** Typical risks include mass deletion, cluster decommission mistakes, rollout deadlock, unsafe maxUpdate, and generator drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **generator change → ApplicationSet policy → create/update/delete → RollingSync groups → multi-cluster rollout** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 14. Cluster Registration, Credentials, Sharding, and Multi-Cluster Management

## Q261. How does Argo CD register an external Kubernetes cluster?

### Answer

**How does Argo CD register an external Kubernetes cluster** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q262. What permissions does argocd cluster add create?

### Answer

**What permissions does argocd cluster add create** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q263. How are cluster credentials stored?

### Answer

**How are cluster credentials stored** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q264. How do bearer-token and client-certificate cluster credentials differ?

### Answer

The correct comparison for **How do bearer-token and client-certificate cluster credentials differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q265. How do exec-provider cluster credentials work?

### Answer

**How do exec-provider cluster credentials work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q266. How do EKS, GKE, and AKS authentication patterns differ?

### Answer

The correct comparison for **How do EKS, GKE, and AKS authentication patterns differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q267. How do you register a cluster declaratively?

### Answer

**How do you register a cluster declaratively** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q268. How do cluster labels and annotations support placement?

### Answer

**How do cluster labels and annotations support placement** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q269. How do you rotate cluster credentials?

### Answer

**How do you rotate cluster credentials** should be implemented declaratively through a reviewed, staged, reversible procedure. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q270. How do you remove a cluster safely?

### Answer

**How do you remove a cluster safely** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q271. How do you prevent a central Argo CD compromise from controlling every cluster?

### Answer

**How do you prevent a central Argo CD compromise from controlling every cluster** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q272. How do project destinations restrict cluster use?

### Answer

**How do project destinations restrict cluster use** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q273. How does application-controller sharding work?

### Answer

**How does application-controller sharding work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The application controller watches Applications and cluster state, requests rendered manifests, compares desired and live resources, executes operations, and publishes status.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q274. How does dynamic cluster distribution work?

### Answer

**How does dynamic cluster distribution work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q275. How do you rebalance cluster shards?

### Answer

**How do you rebalance cluster shards** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster list
argocd cluster get <context-or-server>
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q276. How do you troubleshoot a cluster connection failure?

### Answer

For **How do you troubleshoot a cluster connection failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q277. How do you troubleshoot TLS or proxy failure to a destination cluster?

### Answer

For **How do you troubleshoot TLS or proxy failure to a destination cluster**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster list
argocd cluster get <context-or-server>
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q278. How do you handle clusters with private API endpoints?

### Answer

**How do you handle clusters with private API endpoints** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster get <context-or-server>
kubectl -n argocd get secrets -l argocd.argoproj.io/secret-type=cluster
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q279. How do you monitor cluster credential expiry?

### Answer

**How do you monitor cluster credential expiry** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd cluster list
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q280. How do you design secure hub-and-spoke Argo CD management?

### Answer

**How do you design secure hub-and-spoke Argo CD management** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **manage destination-cluster credentials and distribution securely**.

**Reconciliation flow:** `cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd cluster list
argocd cluster get <context-or-server>
```

**Key risks:** Typical risks include credential compromise, private endpoint failure, expired tokens, shard imbalance, and over-centralized privilege. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret...

**Verification:** Validate **cluster Secret/identity → API TLS/auth → controller shard → Kubernetes discovery/apply/watch** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 15. Resource Tracking, Ownership, Diffing, and Shared Resources

## Q281. How does Argo CD track resources owned by an Application?

### Answer

**How does Argo CD track resources owned by an Application** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get <resource> <name> -n <namespace> -o yaml
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q282. How do label, annotation, and annotation+label tracking methods differ?

### Answer

The correct comparison for **How do label, annotation, and annotation+label tracking methods differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app resources <app>
argocd app diff <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q283. Why can label-based tracking conflict with other tools?

### Answer

**Why can label-based tracking conflict with other tools** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app resources <app>
argocd app diff <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q284. How do installation IDs isolate multiple Argo CD instances?

### Answer

**How do installation IDs isolate multiple Argo CD instances** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q285. How does Argo CD determine whether a resource is orphaned?

### Answer

**How does Argo CD determine whether a resource is orphaned** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl get <resource> <name> -n <namespace> -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q286. How do shared resources create ownership conflicts?

### Answer

**How do shared resources create ownership conflicts** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app resources <app>
argocd app diff <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q287. How does FailOnSharedResource detect conflicts?

### Answer

**How does FailOnSharedResource detect conflicts** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q288. How do you migrate resource-tracking methods?

### Answer

**How do you migrate resource-tracking methods** should be implemented declaratively through a reviewed, staged, reversible procedure. Tracking identifies resources belonging to Applications using annotations, labels, or both.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app resources <app>
argocd app diff <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q289. How do you transfer a resource between Applications safely?

### Answer

**How do you transfer a resource between Applications safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q290. How do you troubleshoot a resource shown under the wrong Application?

### Answer

For **How do you troubleshoot a resource shown under the wrong Application**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get <resource> <name> -n <namespace> -o yaml
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q291. How does normalized live state affect diffing?

### Answer

**How does normalized live state affect diffing** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q292. How do mutating webhooks cause false OutOfSync results?

### Answer

**How do mutating webhooks cause false OutOfSync results** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Git webhooks reduce refresh delay but should use shared-secret validation, TLS, network restrictions, and provider-specific event filtering.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl get <resource> <name> -n <namespace> -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q293. How do defaulted fields affect diffing?

### Answer

**How do defaulted fields affect diffing** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q294. How do managedFields influence diff behavior?

### Answer

**How do managedFields influence diff behavior** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q295. How do you ignore differences by JSON pointer?

### Answer

The correct comparison for **How do you ignore differences by JSON pointer** is based on reconciliation ownership, security, failure modes, scale, and auditability. Ignore rules suppress selected differences caused by defaulting or other controllers, but overly broad rules can hide security or configuration drift.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q296. How do you ignore differences by JQ path expression?

### Answer

The correct comparison for **How do you ignore differences by JQ path expression** is based on reconciliation ownership, security, failure modes, scale, and auditability. Ignore rules suppress selected differences caused by defaulting or other controllers, but overly broad rules can hide security or configuration drift.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl get <resource> <name> -n <namespace> -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q297. How do you ignore differences by managedFields manager?

### Answer

The correct comparison for **How do you ignore differences by managedFields manager** is based on reconciliation ownership, security, failure modes, scale, and auditability. Ignore rules suppress selected differences caused by defaulting or other controllers, but overly broad rules can hide security or configuration drift.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app diff <app>
kubectl get <resource> <name> -n <namespace> -o yaml
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q298. How do system-level and application-level ignore rules differ?

### Answer

The correct comparison for **How do system-level and application-level ignore rules differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q299. How do you avoid hiding meaningful drift?

### Answer

**How do you avoid hiding meaningful drift** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q300. How do you define an enterprise diff-customization policy?

### Answer

**How do you define an enterprise diff-customization policy** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **track ownership and compare live state accurately**.

**Reconciliation flow:** `resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
argocd app resources <app>
```

**Key risks:** Typical risks include label collisions, false drift, hidden meaningful differences, shared ownership, and unsafe tracking migration. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **resource tracking marker → live-resource discovery → normalization/diff → ownership/orphan/shared-resource decisions** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 16. Custom Health Checks, Resource Actions, and Extension Behavior

## Q301. How does Argo CD calculate resource health?

### Answer

**How does Argo CD calculate resource health** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q302. Which Kubernetes resources have built-in health checks?

### Answer

**Which Kubernetes resources have built-in health checks** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-server --since=30m
argocd app get <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q303. How do custom Lua health checks work?

### Answer

**How do custom Lua health checks work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
kubectl -n argocd logs deploy/argocd-server --since=30m
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q304. How do wildcard custom health checks work?

### Answer

**How do wildcard custom health checks work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
kubectl -n argocd logs deploy/argocd-server --since=30m
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q305. How do you test a custom health check?

### Answer

**How do you test a custom health check** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q306. How do custom health checks affect Application health?

### Answer

**How do custom health checks affect Application health** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q307. How do you avoid health checks that remain Progressing forever?

### Answer

**How do you avoid health checks that remain Progressing forever** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q308. How do you define health for a custom resource?

### Answer

**How do you define health for a custom resource** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-server --since=30m
argocd app get <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q309. How do you handle resources managed asynchronously by Operators?

### Answer

**How do you handle resources managed asynchronously by Operators** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q310. How do ignored child-resource health settings work?

### Answer

**How do ignored child-resource health settings work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q311. What are Argo CD resource actions?

### Answer

**What are Argo CD resource actions** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q312. How do built-in and custom resource actions differ?

### Answer

The correct comparison for **How do built-in and custom resource actions differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q313. How do Lua resource actions work?

### Answer

**How do Lua resource actions work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q314. How do you secure resource actions with RBAC?

### Answer

**How do you secure resource actions with RBAC** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD RBAC maps authenticated users or groups to policy rules over Argo CD resources and actions.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-server --since=30m
argocd app get <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q315. How do you troubleshoot an unavailable resource action?

### Answer

For **How do you troubleshoot an unavailable resource action**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q316. How do you expose operational actions without giving full update access?

### Answer

**How do you expose operational actions without giving full update access** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
kubectl -n argocd logs deploy/argocd-server --since=30m
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q317. How do Application actions differ from resource actions?

### Answer

The correct comparison for **How do Application actions differ from resource actions** is based on reconciliation ownership, security, failure modes, scale, and auditability. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q318. How do you govern custom Lua extensions?

### Answer

**How do you govern custom Lua extensions** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get cm argocd-cm -o yaml
kubectl -n argocd logs deploy/argocd-server --since=30m
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q319. How do you version and test health and action scripts?

### Answer

**How do you version and test health and action scripts** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
argocd app actions list <app>
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q320. How do you design extension governance for custom platforms?

### Answer

**How do you design extension governance for custom platforms** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **customize health and operational actions safely**.

**Reconciliation flow:** `resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app actions list <app>
kubectl -n argocd get cm argocd-cm -o yaml
```

**Key risks:** Typical risks include unbounded Progressing, unsafe Lua, overly powerful actions, and extension version drift. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **resource state → built-in/Lua health → Application aggregation, or authorized resource action → API update** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 17. Secrets Management, Encryption, and Sensitive Data in GitOps

## Q321. Why should plaintext Kubernetes Secrets not be stored in Git?

### Answer

**Why should plaintext Kubernetes Secrets not be stored in Git** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
kubectl get externalsecret,sealedsecret -A
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q322. How do Sealed Secrets integrate with Argo CD?

### Answer

**How do Sealed Secrets integrate with Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Sealed Secrets stores encrypted ciphertext in Git and decrypts in-cluster.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get externalsecret,sealedsecret -A
kubectl -n argocd get secrets
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q323. How does External Secrets Operator integrate with Argo CD?

### Answer

**How does External Secrets Operator integrate with Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. External Secrets stores references in Git and retrieves values from external providers at runtime, separating secret value lifecycle from Git.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q324. How does the Secrets Store CSI Driver affect GitOps design?

### Answer

**How does the Secrets Store CSI Driver affect GitOps design** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
kubectl get externalsecret,sealedsecret -A
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q325. How does SOPS-based encryption integrate with manifest generation?

### Answer

**How does SOPS-based encryption integrate with manifest generation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. SOPS encrypts selected structured-data values and typically requires a controlled manifest-generation plugin and KMS/PGP/age credentials.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q326. How do Vault plugins integrate with Argo CD?

### Answer

**How do Vault plugins integrate with Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q327. Where should decryption occur in a GitOps architecture?

### Answer

**Where should decryption occur in a GitOps architecture** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD's core components include the API server, repository server, and application controller; common installations also include Redis, Dex, Notifications, and ApplicationSet.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q328. How do you prevent repo-server from becoming a secret-exfiltration point?

### Answer

**How do you prevent repo-server from becoming a secret-exfiltration point** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q329. How do you manage KMS or Vault credentials used for decryption?

### Answer

**How do you manage KMS or Vault credentials used for decryption** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q330. How do you rotate application secrets without Git history exposure?

### Answer

**How do you rotate application secrets without Git history exposure** should be implemented declaratively through a reviewed, staged, reversible procedure. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q331. How do you rotate repository and cluster credentials?

### Answer

**How do you rotate repository and cluster credentials** should be implemented declaratively through a reviewed, staged, reversible procedure. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q332. How do you prevent secrets from appearing in diffs or logs?

### Answer

**How do you prevent secrets from appearing in diffs or logs** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q333. How do Kubernetes Secret immutability and GitOps interact?

### Answer

**How do Kubernetes Secret immutability and GitOps interact** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q334. How do you manage TLS certificates declaratively?

### Answer

**How do you manage TLS certificates declaratively** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get externalsecret,sealedsecret -A
kubectl -n argocd get secrets
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q335. How do you protect Argo CD's own Secrets?

### Answer

**How do you protect Argo CD's own Secrets** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
kubectl get externalsecret,sealedsecret -A
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q336. How do you back up encrypted GitOps secrets?

### Answer

**How do you back up encrypted GitOps secrets** should be implemented declaratively through a reviewed, staged, reversible procedure. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get externalsecret,sealedsecret -A
kubectl -n argocd get secrets
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q337. How do you recover after losing an encryption key?

### Answer

In the scenario **How do you recover after losing an encryption key**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
kubectl get externalsecret,sealedsecret -A
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q338. How do you scan repositories for committed credentials?

### Answer

**How do you scan repositories for committed credentials** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q339. How do you respond to a secret committed to Git?

### Answer

In the scenario **How do you respond to a secret committed to Git**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. This topic belongs to the Corporate/L3 responsibility to **deliver secrets without storing plaintext or overexposing decryption**.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets
argocd repo list
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q340. How do you create an enterprise GitOps secret-management standard?

### Answer

**How do you create an enterprise GitOps secret-management standard** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd repo list
git grep -n -E '(password|token|secret|BEGIN .*PRIVATE KEY)'
```

**Key risks:** Typical risks include Git history exposure, repo-server exfiltration, lost keys, leaked diffs, and shared decrypt credentials. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **encrypted/external secret declaration → controlled decrypt/fetch → Kubernetes Secret/CSI projection → rotation** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 18. Security Hardening, Supply Chain, and Policy Enforcement

## Q341. How do you harden the Argo CD API server?

### Answer

**How do you harden the Argo CD API server** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q342. How do you harden repo-server?

### Answer

**How do you harden repo-server** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
cosign verify quay.io/argoproj/argocd:<version>
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q343. How do you harden application-controller?

### Answer

**How do you harden application-controller** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The application controller watches Applications and cluster state, requests rendered manifests, compares desired and live resources, executes operations, and publishes status.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q344. How do you enforce non-root and read-only filesystem settings?

### Answer

**How do you enforce non-root and read-only filesystem settings** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
cosign verify quay.io/argoproj/argocd:<version>
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q345. How do NetworkPolicies protect Argo CD components?

### Answer

**How do NetworkPolicies protect Argo CD components** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q346. How do you restrict egress from repo-server?

### Answer

**How do you restrict egress from repo-server** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
cosign verify quay.io/argoproj/argocd:<version>
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q347. How do you verify Argo CD image signatures and provenance?

### Answer

**How do you verify Argo CD image signatures and provenance** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q348. How do you pin container image digests?

### Answer

**How do you pin container image digests** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q349. How do signed Git commits and protected branches improve trust?

### Answer

**How do signed Git commits and protected branches improve trust** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q350. How do admission controllers complement Argo CD?

### Answer

**How do admission controllers complement Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q351. How do Gatekeeper, Kyverno, or ValidatingAdmissionPolicy fit GitOps?

### Answer

**How do Gatekeeper, Kyverno, or ValidatingAdmissionPolicy fit GitOps** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
cosign verify quay.io/argoproj/argocd:<version>
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q352. How do you enforce image-registry allowlists?

### Answer

**How do you enforce image-registry allowlists** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
cosign verify quay.io/argoproj/argocd:<version>
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q353. How do you prevent privileged workloads through GitOps?

### Answer

**How do you prevent privileged workloads through GitOps** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q354. How do you manage policy exceptions?

### Answer

**How do you manage policy exceptions** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
cosign verify quay.io/argoproj/argocd:<version>
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q355. How do you protect webhooks and notification endpoints?

### Answer

**How do you protect webhooks and notification endpoints** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Git webhooks reduce refresh delay but should use shared-secret validation, TLS, network restrictions, and provider-specific event filtering.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
cosign verify quay.io/argoproj/argocd:<version>
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q356. How do you audit Argo CD API and Kubernetes API activity?

### Answer

**How do you audit Argo CD API and Kubernetes API activity** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
cosign verify quay.io/argoproj/argocd:<version>
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q357. How do you isolate tenants with separate instances?

### Answer

**How do you isolate tenants with separate instances** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q358. How do you evaluate third-party extensions and plugins?

### Answer

**How do you evaluate third-party extensions and plugins** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Config management plugins execute repository-controlled or plugin-generated workflows near source credentials.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.securityContext}{"\n"}{end}'
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q359. How do you conduct an Argo CD threat model?

### Answer

**How do you conduct an Argo CD threat model** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **harden Argo CD and the GitOps supply chain**.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q360. How do you create a GitOps supply-chain security baseline?

### Answer

**How do you create a GitOps supply-chain security baseline** is a platform governance and trust-boundary decision, not only an Application YAML choice. Argo CD should use least privilege, protected Git, verified artifacts, restricted egress, network policy, SSO, audit logging, and tightly governed plugins.

**Reconciliation flow:** `trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd auth can-i --list --as=system:serviceaccount:argocd:argocd-application-controller
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include plugin compromise, unrestricted egress, unsigned artifacts, privileged deployments, and tenant breakout. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **trusted Git/image identity → protected components/network/RBAC → admission policy → audit and response** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 19. High Availability, Scalability, Performance, and Capacity

## Q361. Explain Argo CD high-availability architecture.

### Answer

**Explain Argo CD high-availability architecture.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD is largely stateless because durable configuration is in Kubernetes.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q362. Which Argo CD components are stateless?

### Answer

**Which Argo CD components are stateless** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get hpa,pdb
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q363. Why does HA mode use Redis HA?

### Answer

**Why does HA mode use Redis HA** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Redis as a cache rather than the authoritative database.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset
kubectl -n argocd get hpa,pdb
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q364. How do application-controller replicas scale?

### Answer

**How do application-controller replicas scale** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The application controller watches Applications and cluster state, requests rendered manifests, compares desired and live resources, executes operations, and publishes status.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd top pods
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q365. How do repo-server replicas scale?

### Answer

**How do repo-server replicas scale** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd top pods
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q366. How do API server replicas scale?

### Answer

**How do API server replicas scale** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q367. How do ApplicationSet controller replicas behave?

### Answer

**How do ApplicationSet controller replicas behave** should be explained from both Argo CD controller mechanics and enterprise delivery operations. ApplicationSet generates Application resources from list, cluster, Git, SCM, pull-request, matrix, merge, or extension data.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd top pods
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q368. How does leader election affect controllers?

### Answer

**How does leader election affect controllers** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q369. How do controller status and operation processors affect throughput?

### Answer

**How do controller status and operation processors affect throughput** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get hpa,pdb
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q370. How do repo-server parallelism limits affect manifest generation?

### Answer

**How do repo-server parallelism limits affect manifest generation** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd top pods
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q371. How do Git request concurrency and caching affect repositories?

### Answer

**How do Git request concurrency and caching affect repositories** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q372. How do monorepos affect performance?

### Answer

**How do monorepos affect performance** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q373. How do you size CPU and memory for Argo CD components?

### Answer

**How do you size CPU and memory for Argo CD components** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd top pods
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q374. How do you estimate reconciliation load?

### Answer

**How do you estimate reconciliation load** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q375. How do you reduce Kubernetes API pressure?

### Answer

**How do you reduce Kubernetes API pressure** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q376. How do you troubleshoot slow refresh or sync operations?

### Answer

For **How do you troubleshoot slow refresh or sync operations**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q377. How do you troubleshoot repo-server out-of-memory conditions?

### Answer

For **How do you troubleshoot repo-server out-of-memory conditions**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd top pods
kubectl -n argocd get deploy,statefulset
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q378. How do you troubleshoot Redis latency or cache loss?

### Answer

For **How do you troubleshoot Redis latency or cache loss**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Argo CD uses Redis as a cache rather than the authoritative database.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset
kubectl -n argocd get hpa,pdb
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q379. How do you load-test Argo CD safely?

### Answer

**How do you load-test Argo CD safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get hpa,pdb
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q380. How do you create an enterprise Argo CD capacity model?

### Answer

**How do you create an enterprise Argo CD capacity model** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **run Argo CD highly available and capacity-aware**.

**Reconciliation flow:** `HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get deploy,statefulset
kubectl -n argocd get hpa,pdb
```

**Key risks:** Typical risks include OOM, API pressure, monorepo bottlenecks, Redis latency, and processor mis-sizing. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact systems.

**Verification:** Validate **HA replicas/leader election → controller queues/shards → repo rendering/cache → Kubernetes APIs → metrics** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 20. Notifications, Webhooks, and Event Integration

## Q381. Explain Argo CD Notifications architecture.

### Answer

**Explain Argo CD Notifications architecture.** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger run <trigger> <app>
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q382. How do triggers, templates, services, and subscriptions relate?

### Answer

**How do triggers, templates, services, and subscriptions relate** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q383. How do you configure Slack notifications?

### Answer

**How do you configure Slack notifications** should be implemented declaratively through a reviewed, staged, reversible procedure. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q384. How do you configure email notifications?

### Answer

**How do you configure email notifications** should be implemented declaratively through a reviewed, staged, reversible procedure. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q385. How do you configure generic webhooks?

### Answer

**How do you configure generic webhooks** should be implemented declaratively through a reviewed, staged, reversible procedure. Git webhooks reduce refresh delay but should use shared-secret validation, TLS, network restrictions, and provider-specific event filtering.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger run <trigger> <app>
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q386. How do you configure Microsoft Teams Workflows notifications?

### Answer

**How do you configure Microsoft Teams Workflows notifications** should be implemented declaratively through a reviewed, staged, reversible procedure. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications template get
argocd admin notifications trigger run <trigger> <app>
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q387. How do notification Secrets reference sensitive tokens?

### Answer

**How do notification Secrets reference sensitive tokens** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger run <trigger> <app>
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q388. How do annotation-based subscriptions work?

### Answer

**How do annotation-based subscriptions work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q389. How do global subscriptions work?

### Answer

**How do global subscriptions work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications template get
argocd admin notifications trigger run <trigger> <app>
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q390. How do you create custom notification triggers?

### Answer

**How do you create custom notification triggers** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger get
argocd admin notifications template get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q391. How do oncePer expressions prevent duplicate notifications?

### Answer

**How do oncePer expressions prevent duplicate notifications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q392. How do you create custom templates?

### Answer

**How do you create custom templates** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger get
argocd admin notifications template get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q393. How do you include Application metadata in notifications?

### Answer

**How do you include Application metadata in notifications** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger get
argocd admin notifications template get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q394. How do you troubleshoot notifications that do not trigger?

### Answer

For **How do you troubleshoot notifications that do not trigger**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q395. How do you troubleshoot a delivery or authentication failure?

### Answer

For **How do you troubleshoot a delivery or authentication failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q396. How do you test notifications safely?

### Answer

**How do you test notifications safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Notifications evaluates Application state against triggers, renders templates, and sends through configured services.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
argocd admin notifications trigger get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q397. How do Git provider webhooks trigger faster refresh?

### Answer

**How do Git provider webhooks trigger faster refresh** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Git webhooks reduce refresh delay but should use shared-secret validation, TLS, network restrictions, and provider-specific event filtering.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger get
argocd admin notifications template get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q398. How do GitHub, GitLab, and Bitbucket webhook Secrets work?

### Answer

**How do GitHub, GitLab, and Bitbucket webhook Secrets work** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Git webhooks reduce refresh delay but should use shared-secret validation, TLS, network restrictions, and provider-specific event filtering.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger run <trigger> <app>
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q399. How do you avoid notification storms?

### Answer

**How do you avoid notification storms** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger get
argocd admin notifications template get
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q400. How do you define notification ownership and governance?

### Answer

**How do you define notification ownership and governance** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **provide actionable event notifications and webhook refresh**.

**Reconciliation flow:** `Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd admin notifications trigger run <trigger> <app>
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

**Key risks:** Typical risks include notification storms, expired tokens, deprecated endpoints, spoofed webhooks, and missing ownership. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Application state/event → trigger → template → service/Secret → receiver, or Git webhook → refresh** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 21. CI Integration, Promotion, Image Updates, and Progressive Delivery

## Q401. How should CI and Argo CD responsibilities be separated?

### Answer

**How should CI and Argo CD responsibilities be separated** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
kubectl argo rollouts get rollout <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q402. How do you promote immutable artifacts through Git?

### Answer

**How do you promote immutable artifacts through Git** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q403. How do commit-based and pull-request-based promotion differ?

### Answer

The correct comparison for **How do commit-based and pull-request-based promotion differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q404. How do environment branches and environment directories differ?

### Answer

The correct comparison for **How do environment branches and environment directories differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q405. How do you update container image tags safely?

### Answer

**How do you update container image tags safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q406. How does Argo CD Image Updater integrate with Argo CD?

### Answer

**How does Argo CD Image Updater integrate with Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD Image Updater observes registries and updates Git or Application parameters according to version strategies.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q407. How do semver, newest-build, digest, and alphabetical image strategies differ?

### Answer

The correct comparison for **How do semver, newest-build, digest, and alphabetical image strategies differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline -20
argocd app get <app>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q408. How do write-back methods update Git or Application state?

### Answer

**How do write-back methods update Git or Application state** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
kubectl argo rollouts get rollout <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q409. How do you protect image-update credentials?

### Answer

**How do you protect image-update credentials** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q410. How do you prevent mutable-tag deployment surprises?

### Answer

**How do you prevent mutable-tag deployment surprises** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q411. How does Argo Rollouts integrate with Argo CD?

### Answer

**How does Argo Rollouts integrate with Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo Rollouts provides canary and blue-green workload controllers with metric analysis; Argo CD delivers the rollout resource and observes its health.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
kubectl argo rollouts get rollout <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q412. How do blue-green and canary rollouts differ?

### Answer

The correct comparison for **How do blue-green and canary rollouts differ** is based on reconciliation ownership, security, failure modes, scale, and auditability. Argo Rollouts provides canary and blue-green workload controllers with metric analysis; Argo CD delivers the rollout resource and observes its health.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts get rollout <name> -n <namespace>
kubectl argo rollouts promote <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q413. How do analysis templates support progressive delivery?

### Answer

**How do analysis templates support progressive delivery** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q414. How do sync and rollout health interact?

### Answer

**How do sync and rollout health interact** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Application health aggregates resource health.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q415. How do you abort a failed rollout?

### Answer

**How do you abort a failed rollout** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q416. How do you promote a rollout manually?

### Answer

**How do you promote a rollout manually** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline -20
argocd app get <app>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q417. How do you prevent CI and Image Updater from racing?

### Answer

**How do you prevent CI and Image Updater from racing** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD Image Updater observes registries and updates Git or Application parameters according to version strategies.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
kubectl argo rollouts get rollout <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q418. How do you implement promotion across many clusters?

### Answer

**How do you implement promotion across many clusters** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app>
kubectl argo rollouts get rollout <name> -n <namespace>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q419. How do you audit artifact provenance from commit to cluster?

### Answer

**How do you audit artifact provenance from commit to cluster** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl argo rollouts promote <name> -n <namespace>
git log --oneline -20
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

## Q420. How do you create an enterprise promotion strategy?

### Answer

**How do you create an enterprise promotion strategy** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **integrate CI, image promotion, and progressive delivery**.

**Reconciliation flow:** `build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline -20
argocd app get <app>
```

**Key risks:** Typical risks include mutable tags, CI/GitOps races, bad automated updates, rollout-analysis errors, and weak provenance. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **build/test/sign → immutable artifact → Git promotion/Image Updater → Argo CD sync → Rollouts analysis/promotion** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user...

---

# 22. Observability, Metrics, Logs, Audit, and Troubleshooting

## Q421. What metrics do Argo CD components expose?

### Answer

**What metrics do Argo CD components expose** should be implemented declaratively through a reviewed, staged, reversible procedure. Argo CD exposes component and application metrics for reconciliation, Git requests, Kubernetes APIs, queue latency, sync, health, and errors.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q422. How do you monitor application sync and health status?

### Answer

**How do you monitor application sync and health status** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd get pods -o wide
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q423. How do you monitor reconciliation latency?

### Answer

**How do you monitor reconciliation latency** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q424. How do you monitor repo-server performance?

### Answer

**How do you monitor repo-server performance** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q425. How do you monitor Kubernetes API errors?

### Answer

For **How do you monitor Kubernetes API errors**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q426. How do you monitor Redis and cache behavior?

### Answer

**How do you monitor Redis and cache behavior** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD uses Redis as a cache rather than the authoritative database.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q427. How do you collect Argo CD component logs?

### Answer

**How do you collect Argo CD component logs** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q428. How do you enable structured logging?

### Answer

**How do you enable structured logging** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q429. How do you increase logging verbosity safely?

### Answer

**How do you increase logging verbosity safely** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd get pods -o wide
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q430. How do you correlate an Application operation across components?

### Answer

**How do you correlate an Application operation across components** should be explained from both Argo CD controller mechanics and enterprise delivery operations. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd get pods -o wide
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q431. How do you inspect controller operation and refresh queues?

### Answer

**How do you inspect controller operation and refresh queues** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q432. How do you troubleshoot a ComparisonError?

### Answer

For **How do you troubleshoot a ComparisonError**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q433. How do you troubleshoot ManifestGenerationError?

### Answer

For **How do you troubleshoot ManifestGenerationError**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q434. How do you troubleshoot FailedMount or Kubernetes scheduling problems affecting Argo CD?

### Answer

For **How do you troubleshoot FailedMount or Kubernetes scheduling problems affecting Argo CD**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q435. How do you collect an Argo CD diagnostic bundle?

### Answer

**How do you collect an Argo CD diagnostic bundle** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd get pods -o wide
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q436. How do you define Argo CD SLIs and SLOs?

### Answer

**How do you define Argo CD SLIs and SLOs** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q437. How do you create actionable dashboards?

### Answer

**How do you create actionable dashboards** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q438. How do you alert on sustained OutOfSync without noise?

### Answer

**How do you alert on sustained OutOfSync without noise** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q439. How do you preserve evidence during an incident?

### Answer

In the scenario **How do you preserve evidence during an incident**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. A GitOps incident requires pausing harmful reconciliation, preserving Git commits and operation history, restoring a safe live state, correcting Git, and then resuming controlled convergence.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get pods -o wide
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q440. How do you build a Corporate/L3 Argo CD troubleshooting workflow?

### Answer

For **How do you build a Corporate/L3 Argo CD troubleshooting workflow**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **monitor and troubleshoot Argo CD end to end**.

**Reconciliation flow:** `component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include missing context, high-cardinality metrics, noisy OutOfSync alerts, lost evidence, and symptom-only restarts. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or...

**Verification:** Validate **component metrics/logs/events → correlation by Application/operation → alert/dashboard → diagnosis** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 23. Backup, Restore, Disaster Recovery, and Upgrades

## Q441. What Argo CD data must be backed up?

### Answer

**What Argo CD data must be backed up** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
kubectl -n argocd get applications,appprojects,applicationsets
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q442. How do you back up Argo CD custom resources and Secrets?

### Answer

**How do you back up Argo CD custom resources and Secrets** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get crd | grep argoproj.io
argocd version
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q443. How do you back up repository and cluster credentials securely?

### Answer

**How do you back up repository and cluster credentials securely** should be implemented declaratively through a reviewed, staged, reversible procedure. Destination clusters are represented by labeled Secrets containing server and authentication details.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q444. How do you restore Argo CD into a new cluster?

### Answer

**How do you restore Argo CD into a new cluster** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q445. How do you rebuild Redis after failure?

### Answer

For **How do you rebuild Redis after failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. Argo CD uses Redis as a cache rather than the authoritative database.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q446. How do you recover after accidental Application deletion?

### Answer

In the scenario **How do you recover after accidental Application deletion**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. An Application binds a source revision and renderer to a destination cluster/namespace under an AppProject and records comparison, operation, health, and history.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications,appprojects,applicationsets
kubectl -n argocd get secrets,configmaps
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q447. How do you recover after AppProject deletion?

### Answer

In the scenario **How do you recover after AppProject deletion**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. AppProjects constrain source repositories, destinations, resource kinds, roles, and sync windows.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q448. How do you recover after loss of the Argo CD namespace?

### Answer

In the scenario **How do you recover after loss of the Argo CD namespace**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q449. How do you design cross-cluster disaster recovery for Argo CD?

### Answer

**How do you design cross-cluster disaster recovery for Argo CD** is a platform governance and trust-boundary decision, not only an Application YAML choice. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications,appprojects,applicationsets
kubectl -n argocd get secrets,configmaps
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q450. How do you validate a GitOps control-plane restore?

### Answer

**How do you validate a GitOps control-plane restore** should be implemented declaratively through a reviewed, staged, reversible procedure. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get applications,appprojects,applicationsets
kubectl -n argocd get secrets,configmaps
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q451. How do you plan an Argo CD minor-version upgrade?

### Answer

**How do you plan an Argo CD minor-version upgrade** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q452. How do you read version-specific upgrade notes?

### Answer

**How do you read version-specific upgrade notes** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get crd | grep argoproj.io
argocd version
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q453. How do CRD upgrades affect Argo CD?

### Answer

**How do CRD upgrades affect Argo CD** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q454. How do you upgrade HA installations safely?

### Answer

**How do you upgrade HA installations safely** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get crd | grep argoproj.io
argocd version
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q455. How do you upgrade Helm- or Operator-managed installations?

### Answer

**How do you upgrade Helm- or Operator-managed installations** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version
kubectl -n argocd get applications,appprojects,applicationsets
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q456. How do you validate Kubernetes version compatibility?

### Answer

**How do you validate Kubernetes version compatibility** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q457. How do you roll back a failed Argo CD upgrade?

### Answer

**How do you roll back a failed Argo CD upgrade** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q458. How do you manage deprecated configuration and APIs?

### Answer

**How do you manage deprecated configuration and APIs** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get crd | grep argoproj.io
argocd version
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q459. How do you test disaster recovery and upgrade runbooks?

### Answer

**How do you test disaster recovery and upgrade runbooks** should be implemented declaratively through a reviewed, staged, reversible procedure. Only supported minor versions receive security and bug-fix maintenance.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl get crd | grep argoproj.io
argocd version
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

## Q460. How do you define Argo CD RTO and RPO?

### Answer

**How do you define Argo CD RTO and RPO** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **back up, restore, and upgrade the GitOps control plane**.

**Reconciliation flow:** `Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get secrets,configmaps
kubectl get crd | grep argoproj.io
```

**Key risks:** Typical risks include lost credentials, CRD mismatch, untested restore, upgrade behavior changes, and Redis misconception. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external secret or artifact...

**Verification:** Validate **Git plus Argo CD CRs/Secrets → protected backup → restore/install → reconcile clusters → validate; or staged upgrade → postchecks** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the...

---

# 24. OpenShift, Managed Kubernetes, Network, and Platform Integration

## Q461. How does Argo CD integrate with OpenShift?

### Answer

**How does Argo CD integrate with OpenShift** should be explained from both Argo CD controller mechanics and enterprise delivery operations. OpenShift GitOps packages Argo CD with Red Hat lifecycle and integration.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get ingress,service
oc -n argocd get route 2>/dev/null || true
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q462. How do OpenShift Routes expose Argo CD?

### Answer

**How do OpenShift Routes expose Argo CD** should be implemented declaratively through a reviewed, staged, reversible procedure. OpenShift GitOps packages Argo CD with Red Hat lifecycle and integration.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q463. How do OpenShift OAuth and Dex integrate?

### Answer

**How do OpenShift OAuth and Dex integrate** should be explained from both Argo CD controller mechanics and enterprise delivery operations. OpenShift GitOps packages Argo CD with Red Hat lifecycle and integration.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
argocd version --grpc-web
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q464. How do SecurityContextConstraints affect Argo CD?

### Answer

**How do SecurityContextConstraints affect Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Argo CD should use least privilege, protected Git, verified artifacts, restricted egress, network policy, SSO, audit logging, and tightly governed plugins.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q465. How do OpenShift GitOps and upstream Argo CD differ operationally?

### Answer

The correct comparison for **How do OpenShift GitOps and upstream Argo CD differ operationally** is based on reconciliation ownership, security, failure modes, scale, and auditability. OpenShift GitOps packages Argo CD with Red Hat lifecycle and integration.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q466. How do managed Kubernetes control-plane limitations affect Argo CD?

### Answer

**How do managed Kubernetes control-plane limitations affect Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get ingress,service
oc -n argocd get route 2>/dev/null || true
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q467. How do private cluster endpoints affect central Argo CD?

### Answer

**How do private cluster endpoints affect central Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version --grpc-web
kubectl -n argocd get ingress,service
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q468. How do proxies and no_proxy settings affect Argo CD?

### Answer

**How do proxies and no_proxy settings affect Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q469. How do custom certificate authorities affect repositories and clusters?

### Answer

**How do custom certificate authorities affect repositories and clusters** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
argocd version --grpc-web
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q470. How do service meshes affect Argo CD traffic?

### Answer

**How do service meshes affect Argo CD traffic** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd version --grpc-web
kubectl -n argocd get ingress,service
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q471. How do Ingress TLS termination modes affect Argo CD CLI access?

### Answer

**How do Ingress TLS termination modes affect Argo CD CLI access** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q472. How does gRPC-Web help behind certain proxies?

### Answer

**How does gRPC-Web help behind certain proxies** should be explained from both Argo CD controller mechanics and enterprise delivery operations. The Argo CD CLI uses gRPC; some HTTP proxies require correct HTTP/2 routing or `--grpc-web`, while the UI may appear healthy.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
argocd version --grpc-web
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q473. How do you configure an Ingress or Route for both UI and CLI?

### Answer

**How do you configure an Ingress or Route for both UI and CLI** should be implemented declaratively through a reviewed, staged, reversible procedure. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q474. How do NetworkPolicies affect repository and cluster connectivity?

### Answer

**How do NetworkPolicies affect repository and cluster connectivity** should be explained from both Argo CD controller mechanics and enterprise delivery operations. Repository credentials are Kubernetes Secrets recognized by labels.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q475. How do DNS failures manifest in Argo CD?

### Answer

For **How do DNS failures manifest in Argo CD**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get ingress,service
oc -n argocd get route 2>/dev/null || true
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q476. How do you troubleshoot gRPC connection errors?

### Answer

For **How do you troubleshoot gRPC connection errors**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence. The Argo CD CLI uses gRPC; some HTTP proxies require correct HTTP/2 routing or `--grpc-web`, while the UI may appear healthy.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
argocd version --grpc-web
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q477. How do you integrate Argo CD with cloud workload identities?

### Answer

**How do you integrate Argo CD with cloud workload identities** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get networkpolicy
argocd version --grpc-web
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q478. How do you manage platform Operators through Argo CD?

### Answer

**How do you manage platform Operators through Argo CD** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd get ingress,service
oc -n argocd get route 2>/dev/null || true
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q479. How do you avoid ownership conflict with managed-service add-ons?

### Answer

**How do you avoid ownership conflict with managed-service add-ons** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **integrate Argo CD with OpenShift, managed clusters, networking, and platform operators**.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

## Q480. How do you design platform-specific GitOps standards?

### Answer

**How do you design platform-specific GitOps standards** is a platform governance and trust-boundary decision, not only an Application YAML choice. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
oc -n argocd get route 2>/dev/null || true
kubectl -n argocd get networkpolicy
```

**Key risks:** Typical risks include gRPC proxy failure, SCC/security restrictions, custom CA gaps, add-on ownership conflicts, and private endpoint isolation. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and...

**Verification:** Validate **client/source/destination traffic → Route/Ingress/proxy/DNS/TLS → Argo components → platform API/operators** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component metrics/logs, and the real user transaction.

---

# 25. Corporate L3 Incident, Recovery, and Design Scenarios

## Q481. Argo CD deletes critical resources after prune is enabled. How do you respond?

### Answer

For **Argo CD deletes critical resources after prune is enabled. How do you respond**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline --decorate -30
argocd app list
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q482. A bad Git commit makes hundreds of Applications Degraded. How do you recover?

### Answer

In the scenario **A bad Git commit makes hundreds of Applications Degraded. How do you recover**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q483. An Application is Synced and Healthy but users report an outage. How do you investigate?

### Answer

**An Application is Synced and Healthy but users report an outage. How do you investigate** should be explained from both Argo CD controller mechanics and enterprise delivery operations.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
git log --oneline --decorate -30
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q484. Repository credentials expire and all Applications show ComparisonError. How do you respond?

### Answer

For **Repository credentials expire and all Applications show ComparisonError. How do you respond**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q485. A repo-server plugin is compromised. How do you contain it?

### Answer

For **A repo-server plugin is compromised. How do you contain it**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q486. An AppProject change blocks production synchronization. How do you recover?

### Answer

In the scenario **An AppProject change blocks production synchronization. How do you recover**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
git log --oneline --decorate -30
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q487. An RBAC change removes all administrative access. How do you regain control?

### Answer

**An RBAC change removes all administrative access. How do you regain control** should be implemented declaratively through a reviewed, staged, reversible procedure.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q488. SSO is unavailable during a production incident. How do you use break-glass access?

### Answer

For **SSO is unavailable during a production incident. How do you use break-glass access**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline --decorate -30
argocd app list
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q489. An ApplicationSet generator deletes Applications unexpectedly. How do you recover?

### Answer

For **An ApplicationSet generator deletes Applications unexpectedly. How do you recover**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q490. A cluster credential is suspected to be compromised. How do you rotate and investigate?

### Answer

For **A cluster credential is suspected to be compromised. How do you rotate and investigate**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
git log --oneline --decorate -30
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q491. Argo CD continuously reverts an emergency manual fix. How should you respond?

### Answer

In the scenario **Argo CD continuously reverts an emergency manual fix. How should you respond**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q492. A sync hook performs a non-idempotent database migration twice. How do you recover?

### Answer

In the scenario **A sync hook performs a non-idempotent database migration twice. How do you recover**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q493. repo-server is out of memory and synchronization stops. How do you stabilize it?

### Answer

For **repo-server is out of memory and synchronization stops. How do you stabilize it**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline --decorate -30
argocd app list
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q494. Argo CD is available but destination clusters are unreachable. How do you isolate the failure?

### Answer

For **Argo CD is available but destination clusters are unreachable. How do you isolate the failure**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q495. An upgrade changes diff or tracking behavior. How do you recover?

### Answer

**An upgrade changes diff or tracking behavior. How do you recover** should be implemented declaratively through a reviewed, staged, reversible procedure.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
git log --oneline --decorate -30
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q496. Notifications flood the incident channel. How do you stabilize them?

### Answer

For **Notifications flood the incident channel. How do you stabilize them**, isolate whether the first failure is in Git access, manifest generation, comparison, Argo CD authorization, Kubernetes admission, or workload convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
git log --oneline --decorate -30
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q497. A secret is committed to a GitOps repository. How do you contain exposure?

### Answer

**A secret is committed to a GitOps repository. How do you contain exposure** should be explained from both Argo CD controller mechanics and enterprise delivery operations.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q498. How do you lead an Argo CD production incident bridge?

### Answer

In the scenario **How do you lead an Argo CD production incident bridge**, first contain automated impact, preserve Git and cluster evidence, and recover through a controlled source-of-truth change. A GitOps incident requires pausing harmful reconciliation, preserving Git commits and operation history, restoring a safe live state, correcting Git, and then resuming controlled convergence.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app list
argocd app get <app> --show-operation
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q499. What evidence belongs in an Argo CD root-cause analysis?

### Answer

**What evidence belongs in an Argo CD root-cause analysis** should be explained from both Argo CD controller mechanics and enterprise delivery operations. This topic belongs to the Corporate/L3 responsibility to **lead GitOps incident containment and durable recovery**.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
argocd app get <app> --show-operation
kubectl -n argocd logs statefulset/argocd-application-controller --since=1h
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---

## Q500. What should a high-quality Corporate/L3 GitOps runbook contain?

### Answer

**What should a high-quality Corporate/L3 GitOps runbook contain** should be explained from both Argo CD controller mechanics and enterprise delivery operations. GitOps uses declarative version-controlled desired state, automated reconciliation, and pull-based delivery.

**Reconciliation flow:** `pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA`

**L3 approach**

1. Confirm the Git revision, Application or ApplicationSet, AppProject, destination, and impacted users.
2. Trace repository access, rendering, diff, sync, admission, Kubernetes controllers, and health.
3. Preserve logs, events, operation history, rendered manifests, and credential metadata before remediation.
4. Apply a reversible fix to a canary, correct Git, validate the user transaction, and resume reconciliation.

```bash
git log --oneline --decorate -30
argocd app list
```

**Key risks:** Typical risks include further automated damage, evidence loss, credential mishandling, bypassing source of truth, and ownerless actions. Also verify DNS, TLS, proxies, repository and cluster credentials, AppProject restrictions, RBAC claims, finalizers, sync windows, admission policies, CRD ordering, resource quotas, destination capacity, and external...

**Verification:** Validate **pause/contain → preserve Git/Argo/Kubernetes evidence → scope affected Applications/clusters → reversible recovery → source correction → RCA** end to end. Confirm Git revision and signature, rendered output, Application sync/health/operation state, Kubernetes object conditions, events, component...

---
