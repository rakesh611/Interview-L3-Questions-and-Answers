# Kyverno Corporate L3 - 500 Interview Questions and Detailed Practical Answers

> Git-compatible edition: 500 questions, 25 domains, UTF-8, Unix line endings, and balanced code fences.  
> Current context: Kyverno v1.18. Verify the exact patch and Kubernetes compatibility before production changes.

## L3 Method

Identify the policy type, binding, operation, scope, and controller. Trace the AdmissionReview or asynchronous controller flow. Test with the Kyverno CLI, begin in Audit or a canary scope, preserve evidence, validate API latency and workload behavior, then correct the source of truth before Enforce.

## Table of Contents

- [01. Kyverno Fundamentals, Architecture, and Admission Flow](#01-kyverno-fundamentals-architecture-and-admission-flow)
- [02. Installation, Helm, Controllers, High Availability, and Availability Design](#02-installation-helm-controllers-high-availability-and-availability-design)
- [03. Policy Types, CEL Evolution, Legacy APIs, and Migration Strategy](#03-policy-types-cel-evolution-legacy-apis-and-migration-strategy)
- [04. Match, Exclude, Preconditions, Context, Variables, and Data Sources](#04-match-exclude-preconditions-context-variables-and-data-sources)
- [05. Legacy Validate Rules, Patterns, Deny Conditions, and Pod Security](#05-legacy-validate-rules-patterns-deny-conditions-and-pod-security)
- [06. ValidatingPolicy, CEL Expressions, Bindings, and Advanced Validation](#06-validatingpolicy-cel-expressions-bindings-and-advanced-validation)
- [07. Legacy Mutation, Strategic Merge, JSON Patch, and foreach](#07-legacy-mutation-strategic-merge-json-patch-and-foreach)
- [08. MutatingPolicy, CEL-Based Mutation, and Existing Resource Mutation](#08-mutatingpolicy-cel-based-mutation-and-existing-resource-mutation)
- [09. Legacy Generate Rules, Synchronization, Cloning, and UpdateRequests](#09-legacy-generate-rules-synchronization-cloning-and-updaterequests)
- [10. GeneratingPolicy, CEL-Based Generation, Synchronization, and Cloning](#10-generatingpolicy-cel-based-generation-synchronization-and-cloning)
- [11. CleanupPolicy, DeletingPolicy, TTLs, and Resource Lifecycle Automation](#11-cleanuppolicy-deletingpolicy-ttls-and-resource-lifecycle-automation)
- [12. Image Verification, ImageValidatingPolicy, Cosign, Notary, and Attestations](#12-image-verification-imagevalidatingpolicy-cosign-notary-and-attestations)
- [13. Policy Reports, Admission Reports, Background Scans, and Compliance](#13-policy-reports-admission-reports-background-scans-and-compliance)
- [14. PolicyException, Exemption Governance, and Temporary Risk Acceptance](#14-policyexception-exemption-governance-and-temporary-risk-acceptance)
- [15. Kyverno CLI, Policy Testing, Chainsaw, and Shift-Left Validation](#15-kyverno-cli-policy-testing-chainsaw-and-shift-left-validation)
- [16. GitOps, Argo CD, Flux, CI/CD, and Policy as Code](#16-gitops-argo-cd-flux-cicd-and-policy-as-code)
- [17. Security Hardening, RBAC, Webhooks, NetworkPolicy, and Trust Boundaries](#17-security-hardening-rbac-webhooks-networkpolicy-and-trust-boundaries)
- [18. Scaling, Performance, Webhook Latency, and Capacity Planning](#18-scaling-performance-webhook-latency-and-capacity-planning)
- [19. Observability, Metrics, Logs, Events, Dashboards, and Alerting](#19-observability-metrics-logs-events-dashboards-and-alerting)
- [20. Upgrades, CRDs, API Compatibility, Deprecations, and Rollback](#20-upgrades-crds-api-compatibility-deprecations-and-rollback)
- [21. OpenShift, Pod Security, SCCs, Routes, and Enterprise Platform Integration](#21-openshift-pod-security-sccs-routes-and-enterprise-platform-integration)
- [22. Supply Chain, OCI Policies, Sigstore, SBOMs, and Artifact Governance](#22-supply-chain-oci-policies-sigstore-sboms-and-artifact-governance)
- [23. Operations, Governance, Policy Lifecycle, and Production Readiness](#23-operations-governance-policy-lifecycle-and-production-readiness)
- [24. Troubleshooting Webhooks, Policies, Controllers, Reports, and Registries](#24-troubleshooting-webhooks-policies-controllers-reports-and-registries)
- [25. Corporate L3 Kyverno Incident and Design Scenarios](#25-corporate-l3-kyverno-incident-and-design-scenarios)

---

# 01. Kyverno Fundamentals, Architecture, and Admission Flow

## Q001. What problems does Kyverno solve in Kubernetes platform engineering?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q002. Explain Kyverno architecture and its major controllers.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q003. How does Kyverno integrate with Kubernetes dynamic admission control?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q004. Explain the complete AdmissionReview request and response flow.

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **operate Kyverno as a Kubernetes admission and policy-controller platform**. Identify the policy type, binding or scope,...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q005. How does the admission controller differ from the background controller?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning....

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q006. How does the reports controller differ from the background controller?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning....

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q007. How does the cleanup controller operate?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The cleanup controller processes legacy cleanup policies and CEL DeletingPolicy resources and performs scheduled Kubernetes deletions.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q008. How do Policy, ClusterPolicy, and the CEL policy types differ architecturally?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q009. How does Kyverno match resources during admission?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q010. How does Kyverno process CREATE, UPDATE, DELETE, and CONNECT operations?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q011. How does Kyverno use Kubernetes OpenAPI schemas?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q012. How do mutation and validation ordering affect admission?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Kyverno as a Kubernetes admission and policy-controller platform**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q013. How does Kyverno prevent recursive processing of its own changes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q014. How do UpdateRequest resources participate in processing?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Kyverno as a Kubernetes admission and policy-controller platform**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q015. How do AdmissionReport and BackgroundScanReport resources participate in reporting?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate Kyverno as a Kubernetes admission and policy-controller platform**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q016. What is the impact of webhook failurePolicy on cluster availability?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q017. How does Kyverno differ from Kubernetes ValidatingAdmissionPolicy?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q018. How does Kyverno differ from OPA Gatekeeper?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous...

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q019. How do platform and application teams divide Kyverno ownership?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get deploy,pods -n kyverno -o wide
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q020. What are the responsibilities of a Corporate/L3 Kyverno administrator?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `API request → webhook matching → AdmissionReview → policy engine mutation and validation → admission response → reports, UpdateRequests, or background reconciliation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations | grep kyverno
kubectl get deploy,pods -n kyverno -o wide
```

**Risks:** misunderstanding controller ownership, unsafe failurePolicy, webhook recursion, and treating reports as enforcement. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 02. Installation, Helm, Controllers, High Availability, and Availability Design

## Q021. How do you install Kyverno with Helm?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. Helm is...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q022. Why is Helm recommended for production installation?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Helm is the recommended production installation method because Kyverno consists of multiple controllers, CRDs, webhooks, certificates, RBAC, and configurable scaling.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q023. Why should Kyverno use a dedicated namespace?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno list
kubectl -n kyverno get deploy,pdb,svc,secret
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q024. Which Kyverno controllers are mandatory and optional?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q025. How do you configure high availability for the admission controller?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. The admission controller synchronously receives AdmissionReview requests and performs policy validation, mutation, image verification, and exception processing.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q026. How do leader election requirements differ across Kyverno controllers?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno list
kubectl -n kyverno get deploy,pdb,svc,secret
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q027. How do PodDisruptionBudgets protect Kyverno availability?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q028. How do topology spread and anti-affinity improve resilience?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **install highly available Kyverno controllers with controlled webhook scope**. Identify the policy type, binding or scope, responsible...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q029. How do you size admission-controller replicas?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The admission controller synchronously receives AdmissionReview requests and performs policy validation, mutation, image verification, and exception processing.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q030. How do you size background, reports, and cleanup controllers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The cleanup controller processes legacy cleanup policies and CEL DeletingPolicy resources and performs scheduled Kubernetes deletions.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q031. How do you configure resource requests and limits?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **install highly available Kyverno controllers with controlled webhook scope**. Identify the policy type, binding or...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q032. How do you configure webhook timeouts?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno list
kubectl -n kyverno get deploy,pdb,svc,secret
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q033. How do failurePolicy Ignore and Fail differ?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **install highly available Kyverno controllers...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q034. How do namespaceSelector and objectSelector reduce webhook scope?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno list
kubectl -n kyverno get deploy,pdb,svc,secret
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q035. How do matchConditions reduce webhook calls?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q036. How do you install Kyverno in an air-gapped cluster?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q037. How do you mirror Kyverno images and Helm charts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. Helm is the recommended...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q038. How do you validate a Kyverno installation?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q039. How do you troubleshoot a Kyverno controller that is not Ready?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and...

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get deploy,pdb,svc,secret
helm -n kyverno list
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q040. How do you create a production Kyverno installation standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Helm values and CRDs → admission, background, reports, and cleanup Deployments → webhook and certificates → readiness and policy validation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno list
kubectl -n kyverno get deploy,pdb,svc,secret
```

**Risks:** insufficient replicas, missing PDBs, broad webhook scope, certificate failure, and resource starvation. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 03. Policy Types, CEL Evolution, Legacy APIs, and Migration Strategy

## Q041. Explain all Kyverno policy types available in version 1.18.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q042. How do ValidatingPolicy and legacy validate rules differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q043. How do MutatingPolicy and legacy mutate rules differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q044. How do GeneratingPolicy and legacy generate rules differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q045. How do DeletingPolicy and legacy cleanup policies differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q046. How does ImageValidatingPolicy differ from verifyImages rules?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q047. Why are ClusterPolicy and CleanupPolicy considered legacy policy types?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q048. What is the deprecation timeline for legacy policy types?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q049. How does CEL improve alignment with Kubernetes policy APIs?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q050. How does JMESPath differ from CEL in Kyverno?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q051. How do namespaced and cluster-scoped policy types differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q052. How do policy bindings affect CEL policy evaluation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q053. How do policy type APIs and CRDs change upgrade planning?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q054. How do you inventory legacy policies before migration?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q055. How do you translate a validate rule into ValidatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q056. How do you translate a mutate rule into MutatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q057. How do you translate a generate rule into GeneratingPolicy?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q058. How do you translate cleanup policies into DeletingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
kubectl api-resources | grep -i kyverno
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q059. How do you test semantic equivalence during migration?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q060. How do you design an enterprise policy-type migration roadmap?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **govern legacy and CEL-based policy APIs through migration**. Identify the policy type, binding or scope, responsible...

**Flow:** `policy intent → legacy JMESPath or CEL policy type → binding and controller evaluation → enforcement, mutation, generation, deletion, or image verification`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl api-resources | grep -i kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** deprecated APIs, semantic migration drift, missing bindings, and incompatible CRD upgrades. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 04. Match, Exclude, Preconditions, Context, Variables, and Data Sources

## Q061. How do match and exclude blocks work in legacy policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q062. How do any and all resource filters differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q063. How do kinds, names, namespaces, labels, and annotations affect matching?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q064. How do subjects, roles, and clusterRoles affect matching?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q065. Why can user information not be used in background scans?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q066. How do operations and subresources affect policy matching?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q067. How do preconditions reduce unnecessary rule processing?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q068. How do Any and All preconditions differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q069. How do request.object and request.oldObject differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q070. How does request.operation affect policy logic?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q071. How do context variables work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q072. How do ConfigMap context lookups work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q073. How do Kubernetes API context calls work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q074. How do image-registry context calls work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q075. How do external HTTP context calls work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q076. How do variable substitution and nested variables work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q077. How do JMESPath functions extend policy logic?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q078. How do CEL libraries extend new policy types?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q079. How do you prevent expensive or unsafe external context calls?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q080. How do you create enterprise matching and context standards?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **select resources and external context safely and efficiently**. Identify the policy type, binding or scope, responsible...

**Flow:** `AdmissionReview or scan resource → match and exclude constraints → preconditions and variables → context lookup → rule evaluation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get clusterpolicy,policy -A
```

**Risks:** wildcard scope, expensive API or HTTP calls, missing background context, and untrusted external data. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 05. Legacy Validate Rules, Patterns, Deny Conditions, and Pod Security

## Q081. How do validate rules work in Policy and ClusterPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q082. How do validationFailureAction Audit and Enforce differ?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q083. How do pattern and anyPattern validation differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope,...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q084. How do anchors affect validation patterns?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q085. How do conditional and equality anchors differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope,...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q086. How do global and existence anchors differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope,...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q087. How do deny conditions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q088. How do Any and All deny conditions differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope,...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q089. How do foreach validation rules work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q090. How do you validate nested arrays safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q091. How do you require labels and annotations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q092. How do you restrict container security contexts?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q093. How do you restrict host namespaces and host paths?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q094. How do you restrict privileged containers and Linux capabilities?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q095. How do you enforce resource requests and limits?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q096. How do you enforce approved StorageClasses?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q097. How do you validate Ingress or Route TLS settings?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod security rules**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q098. How do Kyverno Pod Security checks work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q099. How do you troubleshoot a validate rule that rejects valid resources?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **write reliable legacy validation and Pod...

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kyverno test ./tests
kubectl get clusterpolicy,policy -A
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q100. How do you create a legacy validation-policy quality standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `resource match → patterns, foreach, or deny conditions → Audit or Enforce response → report result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: check-team
      match:
        any:
          - resources:
              kinds: [Deployment]
      validate:
        message: label team is required
        pattern:
          metadata:
            labels:
              team: "?*"
```

```bash
kubectl get clusterpolicy,policy -A
kyverno test ./tests
```

**Risks:** anchor mistakes, array matching errors, broad deny conditions, and unexpected webhook rejection. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 06. ValidatingPolicy, CEL Expressions, Bindings, and Advanced Validation

## Q101. Explain the ValidatingPolicy resource.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q102. How does Kyverno ValidatingPolicy extend Kubernetes ValidatingAdmissionPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q103. How do matchConstraints work in ValidatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q104. How do validations and message expressions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q105. How do failurePolicy and validationActions work?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q106. How do ValidatingPolicyBinding resources select policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q107. How do binding matchResources restrict policy scope?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q108. How do CEL object and oldObject variables work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q109. How do CEL request and namespaceObject variables work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q110. How do CEL authorizer functions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q111. How do Kyverno CEL libraries work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. CEL-based Kyverno policies align...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q112. How do you validate JSON payloads outside Kubernetes resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q113. How do you perform cross-resource validation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q114. How do you use variables in ValidatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q115. How do you generate dynamic validation messages?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q116. How do you write deletion-protection validation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q117. How do you validate updates differently from creates?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **write CEL-first ValidatingPolicy and binding logic**. Identify the policy type, binding or scope,...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q118. How do you benchmark CEL validation performance?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get validatingpolicies.policies.kyverno.io -A
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q119. How do you troubleshoot a CEL compilation error?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18...

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q120. How do you create an enterprise CEL-validation standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `matchConstraints and binding → CEL variables and validations → message and action → admission or background result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ValidatingPolicy
metadata:
  name: require-team-label
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: [apps]
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [deployments]
  validations:
    - expression: has(object.metadata.labels) && 'team' in object.metadata.labels
      message: label team is required
```

```bash
kubectl get validatingpolicies.policies.kyverno.io -A
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** CEL compilation failure, broad binding, authorizer misuse, and semantic differences from legacy rules. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 07. Legacy Mutation, Strategic Merge, JSON Patch, and foreach

## Q121. How do legacy mutate rules work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q122. How does patchStrategicMerge mutation work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q123. How does patchesJson6902 mutation work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q124. How do strategic merge anchors work during mutation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q125. How do you add default labels and annotations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q126. How do you mutate Pod security contexts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q127. How do you add topology spread constraints?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q128. How do you inject environment variables safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q129. How do you add volumes and volume mounts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q130. How do you rewrite image registries?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q131. How do you mutate image tags to digests?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q132. How do foreach mutation rules work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q133. How do you mutate all containers, init containers, and ephemeral containers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q134. How do you avoid overwriting user-provided values?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q135. How do you mutate only on CREATE operations?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q136. How do you use mutateExistingOnPolicyUpdate?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q137. How do mutate-existing rules differ from admission mutation?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **perform safe admission and existing-resource mutation with legacy rules**. Identify the policy type,...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q138. How do you troubleshoot conflicting mutating webhooks?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q139. How do you troubleshoot mutation that creates GitOps drift?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. GitOps provides review and version history, but Kyverno mutation and generated resources require...

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kubectl get updaterequests -n kyverno
kyverno apply policy.yaml --resource resource.yaml
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q140. How do you create a legacy mutation-policy quality standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `resource match → strategic merge or JSON patch → mutated AdmissionResponse or UpdateRequest → API-server persistence`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-owner-label
spec:
  rules:
    - name: add-owner
      match:
        any:
          - resources:
              kinds: [Pod]
      mutate:
        patchStrategicMerge:
          metadata:
            labels:
              +(owner): platform
```

```bash
kyverno apply policy.yaml --resource resource.yaml
kubectl get updaterequests -n kyverno
```

**Risks:** field ownership conflict, webhook ordering, GitOps drift, array replacement, and mutation loops. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 08. MutatingPolicy, CEL-Based Mutation, and Existing Resource Mutation

## Q141. Explain the MutatingPolicy resource.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q142. How does Kyverno MutatingPolicy extend Kubernetes MutatingAdmissionPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q143. How do JSONPatch expressions work in MutatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q144. How do ApplyConfiguration expressions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q145. How do matchConstraints and matchConditions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q146. How do MutatingPolicyBinding resources work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q147. How do you add metadata using CEL mutation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q148. How do you mutate Pod containers with CEL?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q149. How do you mutate arrays safely with CEL?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q150. How do you preserve user-owned fields?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q151. How do you mutate existing resources with MutatingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q152. How does evaluation.mutateExisting work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q153. How do you select target resources for mutate-existing behavior?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q154. How do background-controller permissions affect mutation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q155. How do you prevent mutation loops?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q156. How do you debug generated MutatingAdmissionPolicy resources?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q157. How do you test CEL mutation with Kyverno CLI?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. CEL-based Kyverno policies align...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q158. How do you migrate strategic-merge rules to CEL mutation?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q159. How do you troubleshoot mutate-existing UpdateRequests?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kubectl get mutatingpolicies.policies.kyverno.io -A
kyverno test ./tests
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q160. How do you create an enterprise MutatingPolicy standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **perform CEL-based mutation with clear bindings and field ownership**. Identify the policy type, binding or scope,...

**Flow:** `MutatingPolicy and binding → CEL JSONPatch or ApplyConfiguration → admission or mutate-existing target update`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: MutatingPolicy
metadata:
  name: add-owner-label
spec:
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE]
        resources: [pods]
  mutations:
    - patchType: JSONPatch
      jsonPatch:
        expression: >-
          [JSONPatch{op: 'add', path: '/metadata/labels/owner', value: 'platform'}]
```

```bash
kyverno test ./tests
kubectl get mutatingpolicies.policies.kyverno.io -A
```

**Risks:** invalid patch expressions, overwritten user fields, binding errors, and background RBAC failure. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 09. Legacy Generate Rules, Synchronization, Cloning, and UpdateRequests

## Q161. How do legacy generate rules work?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q162. How do data and clone generation differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q163. How do cloneList generate rules work?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q164. How do synchronize true and false differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q165. How do generated-resource ownership labels work?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q166. How do UpdateRequest resources drive generation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q167. How do generateExisting and policy updates affect existing triggers?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q168. How do you generate default NetworkPolicies for namespaces?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q169. How do you generate ResourceQuotas and LimitRanges?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q170. How do you clone Secrets or ConfigMaps safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q171. How do you generate RoleBindings based on namespace labels?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q172. How do you generate resources across namespaces?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q173. How do background-controller RBAC permissions affect generate rules?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning.

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q174. How do server-side apply settings affect generated resources?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q175. How do GitOps tools interact with generated resources?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. GitOps provides review and version history, but Kyverno mutation and generated resources require deliberate ownership and server-side diff configuration.

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q176. How do you preserve generated resources when deleting a policy?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q177. How do you troubleshoot UpdateRequests stuck Pending?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q178. How do you troubleshoot generated resources that are not synchronized?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q179. How do you prevent generated-resource ownership conflicts?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope,...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl get updaterequests -n kyverno
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q180. How do you create an enterprise generate-rule standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **generate or clone resources through background reconciliation**. Identify the policy type, binding or scope, responsible...

**Flow:** `trigger admission or policy update → UpdateRequest → background controller → create, clone, or synchronize downstream resource`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-default-deny
spec:
  rules:
    - name: generate-network-policy
      match:
        any:
          - resources:
              kinds: [Namespace]
      generate:
        synchronize: true
        apiVersion: networking.k8s.io/v1
        kind: NetworkPolicy
        name: default-deny
        namespace: '{{request.object.metadata.name}}'
        data:
          spec:
            podSelector: {}
            policyTypes: [Ingress, Egress]
```

```bash
kubectl -n kyverno logs deploy/kyverno-background-controller --since=30m
kubectl get updaterequests -n kyverno
```

**Risks:** excessive permissions, duplicate resources, ownership conflict, and stuck UpdateRequests. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 10. GeneratingPolicy, CEL-Based Generation, Synchronization, and Cloning

## Q181. Explain the GeneratingPolicy resource.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q182. How does GeneratingPolicy use CEL expressions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q183. How do GeneratingPolicy and legacy generate rules differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q184. How do matchConstraints select triggers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q185. How do generated resource expressions work?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q186. How do you generate multiple resources from a list?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q187. How do you clone ConfigMaps and Secrets with CEL accessors?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q188. How does synchronization work in GeneratingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q189. How do trigger updates affect generated resources?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q190. How do source-resource updates affect cloned resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q191. How does orphanDownstreamOnPolicyDelete work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q192. How do you preserve downstream resources during policy deletion?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q193. How do PolicyExceptions apply to GeneratingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q194. How do background-controller permissions affect GeneratingPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning.

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q195. How do you generate namespaced and cluster-scoped resources?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q196. How do you avoid duplicate generated-resource names?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q197. How do you test GeneratingPolicy in CI?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q198. How do you migrate legacy generate rules to GeneratingPolicy?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q199. How do you troubleshoot generation evaluation errors?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get updaterequests -n kyverno
kubectl get generatingpolicies.policies.kyverno.io -A
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q200. How do you create an enterprise GeneratingPolicy standard?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **use CEL-based generation and synchronization safely**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `GeneratingPolicy trigger and CEL expressions → background controller → downstream resource creation or cloning → synchronization`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get generatingpolicies.policies.kyverno.io -A
kubectl get updaterequests -n kyverno
```

**Risks:** duplicate names, source access failure, orphan behavior, and PolicyException scope. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC, external registry...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 11. CleanupPolicy, DeletingPolicy, TTLs, and Resource Lifecycle Automation

## Q201. How do legacy CleanupPolicy and ClusterCleanupPolicy resources work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q202. How does the cleanup controller schedule deletion?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The cleanup controller processes legacy cleanup policies and CEL DeletingPolicy resources and performs scheduled Kubernetes deletions.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q203. How do cleanup match and exclude rules work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q204. How do cleanup conditions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q205. How does the cleanup.kyverno.io/ttl label work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q206. How do you clean up completed Jobs?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q207. How do you clean up expired development namespaces?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q208. How do you clean up stale Pods or temporary resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q209. How do you prevent deletion of protected resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q210. How do cleanup policies interact with finalizers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q211. Explain the CEL-based DeletingPolicy resource.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q212. How does DeletingPolicy schedule resource deletion?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q213. How do CEL match conditions select deletion targets?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q214. How do PolicyExceptions affect deletion policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q215. How do you audit deletion events?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q216. How do you test cleanup policies safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get deletingpolicies.policies.kyverno.io -A
kubectl get cleanuppolicies,clustercleanuppolicies -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q217. How do you perform a dry-run assessment before deletion?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q218. How do you migrate CleanupPolicy to DeletingPolicy?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope,...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q219. How do you troubleshoot cleanup CronJobs or deletion failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q220. How do you create enterprise cleanup and retention standards?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **delete expired resources through controlled scheduled policies**. Identify the policy type, binding or scope, responsible...

**Flow:** `CleanupPolicy or DeletingPolicy schedule → cleanup controller or CronJob → match and condition → Kubernetes delete → event`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: DeletingPolicy
metadata:
  name: delete-old-jobs
spec:
  schedule: '0 * * * *'
  matchConstraints:
    resourceRules:
      - apiGroups: [batch]
        apiVersions: [v1]
        resources: [jobs]
  conditions:
    - expression: object.status.completionTime != null
```

```bash
kubectl get cleanuppolicies,clustercleanuppolicies -A
kubectl get deletingpolicies.policies.kyverno.io -A
```

**Risks:** premature deletion, finalizer blockage, broad selectors, clock assumptions, and missing audit evidence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 12. Image Verification, ImageValidatingPolicy, Cosign, Notary, and Attestations

## Q221. Explain Kyverno image verification architecture.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable digests. Kyverno is a Kubernetes-native policy engine which validates, mutates,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q222. How do verifyImages rules work in legacy ClusterPolicy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q223. How does ImageValidatingPolicy differ from verifyImages?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q224. How do image signatures and attestations differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q225. How does Cosign key-based verification work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q226. How does keyless Sigstore verification work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q227. How do certificate identities and issuers restrict keyless verification?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q228. How does Notary v2 verification work?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q229. How do you verify in-toto attestations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q230. How do you validate SBOM or vulnerability attestations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q231. How do image digest mutation and verification interact?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q232. How does imagePullSecrets reuse work in Kyverno 1.18?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q233. How do private registry credentials affect verification?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q234. How do you restrict images to approved registries?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q235. How do you prevent mutable tags?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q236. How do you require signed images in Enforce mode?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q237. How do you introduce image verification safely in Audit mode?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable digests.

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q238. How do you troubleshoot registry authentication or timeout failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q239. How do you troubleshoot signature verification failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q240. How do you create an enterprise software-supply-chain policy?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **verify image provenance, signatures, attestations, and registries**. Identify the policy type, binding or scope,...

**Flow:** `Pod or controller image reference → registry resolution and credentials → signature or attestation verification → digest mutation and admission decision`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1
kind: ImageValidatingPolicy
metadata:
  name: require-signed-images
spec:
  validationActions: [Audit]
  matchConstraints:
    resourceRules:
      - apiGroups: ['']
        apiVersions: [v1]
        operations: [CREATE, UPDATE]
        resources: [pods]
  imageExtractors:
    Pod:
      - path: /spec/containers/*/image
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get imagevalidatingpolicies.policies.kyverno.io -A
```

**Risks:** registry latency, credential leakage, mutable tags, compromised signing keys, and emergency-image lockout. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 13. Policy Reports, Admission Reports, Background Scans, and Compliance

## Q241. Explain Kyverno PolicyReport and ClusterPolicyReport resources.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q242. How do admission and background report pipelines differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q243. How do AdmissionReport and ClusterAdmissionReport resources work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q244. How do BackgroundScanReport resources work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q245. How do EphemeralReport resources participate in aggregation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q246. How does the reports controller aggregate results?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The reports controller performs background scans and aggregates temporary admission and background results into PolicyReport resources.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q247. How do pass, fail, warn, error, and skip results differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q248. How do Audit and Enforce decisions appear in reports?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q249. How do background scans evaluate existing resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q250. Which policy rules support background scanning?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q251. Why do background scans exclude admission-user information?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q252. How do you disable reports for a specific policy?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q253. How do you limit allowed report result types?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q254. How do you use OpenReports-compatible reports?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q255. How do you query policy violations with kubectl?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q256. How do you export reports to Prometheus or external systems?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q257. How do you handle report-resource growth?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q258. How do you troubleshoot stale or missing reports?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q259. How do you prove compliance using policy reports?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyReport is namespaced and ClusterPolicyReport is cluster-scoped. Reports show compliance results but do not themselves block admission.

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicyreports
kubectl get policyreports -A
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q260. How do you create an enterprise compliance-reporting workflow?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **produce useful admission and background compliance reports**. Identify the policy type, binding or scope, responsible...

**Flow:** `admission or background evaluation → temporary report resource → reports controller aggregation → PolicyReport or external reporting`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
```

**Risks:** report explosion, stale scans, disabled reports, etcd pressure, and misinterpreted Audit results. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 14. PolicyException, Exemption Governance, and Temporary Risk Acceptance

## Q261. Explain the PolicyException resource.

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q262. Why are PolicyExceptions disabled by default?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q263. How do legacy PolicyException policyName and ruleNames work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry. ClusterPolicy and...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q264. How do CEL PolicyException policyRefs work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry. CEL-based Kyverno policies...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q265. How do exception match conditions work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q266. How do namespaced exception controls work?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q267. How do PolicyExceptions affect admission evaluation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q268. How do PolicyExceptions affect background reports?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q269. How do skipped results appear in PolicyReports?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q270. How do you create a narrow workload-specific exception?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q271. How do you time-limit an exception?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q272. How do you combine exceptions with cleanup or TTL policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q273. How do you require approval metadata on exceptions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q274. How do you validate PolicyException resources with Kyverno itself?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry. Kyverno is a Kubernetes-native...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q275. How do Kubernetes RBAC and GitOps protect exceptions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. GitOps provides review and version history, but Kyverno mutation and generated resources require deliberate ownership and server-side diff configuration.

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q276. How do you prevent wildcard exceptions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q277. How do you audit exception usage?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q278. How do you identify expired or unused exceptions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible controller, admission operation,...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
kubectl get policyexceptions -A
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q279. How do you respond to an abused PolicyException?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q280. How do you create an enterprise policy-exception process?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern narrow, temporary, and auditable exemptions**. Identify the policy type, binding or scope, responsible...

**Flow:** `PolicyException creation → RBAC and validation → resource and policy match → skipped enforcement or report result → expiration review`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: policies.kyverno.io/v1alpha1
kind: PolicyException
metadata:
  name: temporary-exception
  namespace: policy-exceptions
spec:
  policyRefs:
    - name: require-team-label
      kind: ValidatingPolicy
  matchConditions:
    - name: approved-workload
      expression: object.metadata.name == 'legacy-app'
```

```bash
kubectl get policyexceptions -A
kubectl auth can-i create policyexceptions.policies.kyverno.io -n <namespace>
```

**Risks:** wildcard exemptions, permanent risk acceptance, missing approval metadata, and untracked usage. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 15. Kyverno CLI, Policy Testing, Chainsaw, and Shift-Left Validation

## Q281. What capabilities does the Kyverno CLI provide?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q282. How do you install and version the Kyverno CLI?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q283. How does kyverno apply evaluate policies locally?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q284. How does kyverno test use test values and expected results?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q285. How do you supply variables and context data to CLI tests?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission operation, permissions,...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q286. How do you test mutate rules with the CLI?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission operation, permissions,...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q287. How do you test generate rules with the CLI?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q288. How do you test image verification policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable digests.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q289. How do you test CEL policy types?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18 for validation, mutation, generation, deletion, and image verification.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q290. How do you evaluate JSON payloads outside Kubernetes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission operation, permissions,...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q291. How do you test policies against live cluster resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission operation, permissions,...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q292. How do you use kyverno jp for JMESPath queries?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q293. How do you use Kyverno CEL tooling?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. CEL-based Kyverno policies align...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q294. How do Chainsaw tests validate cluster behavior?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission operation, permissions,...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q295. How do you test webhook and background behavior separately?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q296. How do you create negative and boundary test cases?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q297. How do you test autogen-generated rules?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q298. How do you integrate Kyverno tests into pull requests?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./tests
kyverno version
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q299. How do you troubleshoot differences between CLI and cluster evaluation?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**....

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q300. How do you create an enterprise policy-testing framework?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **test policies before cluster admission**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `policy and resource fixtures → Kyverno CLI or Chainsaw evaluation → expected pass, fail, mutation, generation, or verification result → CI gate`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno version
kyverno test ./tests
```

**Risks:** CLI and cluster context differences, missing variables, weak negative tests, and version mismatch. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 16. GitOps, Argo CD, Flux, CI/CD, and Policy as Code

## Q301. How do you manage Kyverno policies through GitOps?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. GitOps provides review and...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q302. How do you structure repositories for cluster and application policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q303. How do you separate platform policies from tenant policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q304. How do you promote policies across development, test, and production?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q305. How do you introduce Audit policies before Enforce?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q306. How do you use Kyverno CLI in CI pipelines?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q307. How do you use policy reports as deployment gates?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyReport is namespaced and ClusterPolicyReport is cluster-scoped. Reports show compliance results but do not themselves block admission.

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q308. How do Kyverno mutations affect Argo CD drift?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q309. How does Argo CD ServerSideDiff reduce mutation drift?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q310. How do Flux and Kyverno generated resources interact?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q311. How do you prevent GitOps and Kyverno ownership conflicts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. GitOps provides review and...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q312. How do you manage generated resources in Git?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q313. How do you use Helm or Kustomize for policy packaging?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Helm is the recommended production installation method because Kyverno consists of multiple controllers, CRDs, webhooks, certificates, RBAC, and configurable scaling.

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q314. How do you version policy bundles?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q315. How do you sign policy commits and artifacts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q316. How do you implement policy pull-request approvals?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q317. How do you handle emergency policy changes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q318. How do you roll back a bad policy release?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get applications.argoproj.io -A 2>/dev/null || true
kyverno test ./policies
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q319. How do you audit automated policy changes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q320. How do you create an enterprise policy-as-code workflow?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **manage policy lifecycle through reviewed GitOps delivery**. Identify the policy type, binding or scope, responsible...

**Flow:** `Git policy bundle → CI tests and approval → Argo CD, Flux, Helm, or Kustomize deployment → Audit observation → Enforce promotion`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno test ./policies
kubectl get applications.argoproj.io -A 2>/dev/null || true
```

**Risks:** mutation drift, generated-resource conflicts, bad policy fan-out, and emergency source-of-truth divergence. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 17. Security Hardening, RBAC, Webhooks, NetworkPolicy, and Trust Boundaries

## Q321. How do you harden the Kyverno namespace?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q322. How do you apply least privilege to Kyverno service accounts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q323. How do admission-controller and background-controller RBAC needs differ?

### Answer

Compare the options through admission timing, policy language, scope, permissions, reports, performance, migration, and failure behavior. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning....

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q324. How do generate and mutate-existing rules expand permissions?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q325. How do you restrict who can create policies?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q326. How do you restrict who can create PolicyExceptions?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected with RBAC, Git review, validation, and expiry.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q327. How do you protect Kyverno webhook configurations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. A fail-closed admission webhook...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q328. How do you protect webhook TLS certificates?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q329. How do NetworkPolicies protect Kyverno controllers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q330. How do you restrict egress for external context and registry calls?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q331. How do you protect ConfigMaps used as policy data sources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q332. How do you protect registry credentials?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q333. How do you prevent policy-based privilege escalation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q334. How do you prevent malicious generate rules?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q335. How do you prevent unsafe HTTP context calls?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data sources, and permissions**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q336. How do you secure dumpPayload troubleshooting?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q337. How do you audit Kyverno administrative actions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q338. How do you threat-model Kyverno?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q339. How do you respond to a compromised policy repository?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **harden Kyverno identities, webhooks, data...

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl get networkpolicy -n kyverno
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q340. How do you create an enterprise Kyverno security baseline?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted policy source → restricted RBAC and webhook configuration → network and certificate controls → audited processing`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-kyverno
  namespace: kyverno
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```

```bash
kubectl auth can-i --list --as=system:serviceaccount:kyverno:kyverno-background-controller
kubectl get networkpolicy -n kyverno
```

**Risks:** policy privilege escalation, HTTP exfiltration, compromised registry credentials, certificate theft, and broad exceptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 18. Scaling, Performance, Webhook Latency, and Capacity Planning

## Q341. What factors drive Kyverno resource consumption?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q342. Why is node or Pod count alone insufficient for sizing?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q343. How do admission request rate and policy complexity affect latency?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q344. How do wildcard policies affect webhook and engine load?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q345. How do external API and registry calls affect latency?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q346. How do image verification rules affect performance?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable digests.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q347. How do background scans affect reports-controller load?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The reports controller performs background scans and aggregates temporary admission and background results into PolicyReport resources. Background scanning periodically evaluates existing resources for...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q348. How do generate and mutate-existing rules affect background-controller load?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. The background controller handles generate and mutate-existing operations through UpdateRequest resources; it is distinct from background compliance scanning.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q349. How do reports and Events affect API-server and etcd load?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q350. How do you measure Kyverno admission latency?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q351. How do you measure policy execution duration?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q352. How do you identify a slow policy or rule?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q353. How do you reduce webhook scope?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q354. How do matchConditions improve scale?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q355. How do you scale each Kyverno controller independently?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q356. How do you prevent report accumulation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q357. How do you tune worker counts and queues?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q358. How do you load-test Kyverno safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q359. How do you establish capacity headroom?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **capacity-plan admission latency and independent controllers**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
kubectl top pods -n kyverno --containers
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q360. How do you create an enterprise Kyverno capacity model?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `request and policy complexity → webhook queue and engine → reports or background work → API-server and etcd effects → metrics and scaling`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl top pods -n kyverno --containers
kubectl get --raw /metrics 2>/dev/null | grep kyverno | head
```

**Risks:** latency spikes, wildcard policies, report growth, registry calls, queue backlog, and controller imbalance. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 19. Observability, Metrics, Logs, Events, Dashboards, and Alerting

## Q361. What metrics does Kyverno expose?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q362. How do you monitor admission request counts and latency?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q363. How do you monitor policy and rule execution results?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q364. How do you monitor controller reconciliations and queues?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q365. How do you monitor webhook health?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q366. How do you monitor policy changes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q367. How do you monitor policy reports and violations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. PolicyReport is namespaced and ClusterPolicyReport is cluster-scoped. Reports show compliance results but do not themselves block admission.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q368. How do you monitor image verification failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q369. How do you monitor background scan duration?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q370. How do you monitor cleanup and deletion activity?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q371. How do you configure Prometheus scraping?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope,...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q372. How do you create Grafana dashboards for Kyverno?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q373. How do you alert on admission-controller unavailability?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The admission controller synchronously receives AdmissionReview requests and performs policy validation, mutation, image verification, and exception processing.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q374. How do you alert on sustained policy errors?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q375. How do you avoid high-cardinality Kyverno metrics?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q376. How do you configure structured logging?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope,...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q377. How do you increase log verbosity safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy execution, and compliance**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q378. How do Kubernetes Events support troubleshooting?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **monitor Kyverno availability, policy...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q379. How do you preserve Kyverno evidence during an incident?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. During an incident, preserve policy versions, webhook configuration, controller logs, AdmissionReview context, events, reports,...

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q380. How do you define Kyverno SLIs and SLOs?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `controller metrics, logs, Events, and reports → Prometheus and dashboards → SLO alert → investigation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp | grep -i kyverno
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** missing metrics, high cardinality, noisy policy errors, hidden webhook degradation, and evidence loss. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 20. Upgrades, CRDs, API Compatibility, Deprecations, and Rollback

## Q381. How do you plan a Kyverno upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q382. How does Kyverno's supported-release policy affect upgrades?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q383. How do Kubernetes compatibility ranges affect upgrade planning?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q384. Why must Kyverno upgrades include CRD and supporting resource changes?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q385. Why is changing only container image tags unsafe?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q386. How do you back up policies, exceptions, reports, and configuration?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q387. How do you upgrade Kyverno with Helm?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. Helm is...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q388. How do you upgrade air-gapped installations?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q389. How do you validate CRDs before and after upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q390. How do you validate webhook configurations after upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q391. How do you test legacy and CEL policies after upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion. CEL-based...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q392. How do you handle policy schema validation changes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q393. How do you migrate deprecated policy APIs during upgrades?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q394. How do you detect breaking behavior in image verification?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable digests.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q395. How do you test report-controller behavior after upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q396. How do you perform a canary Kyverno upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q397. How do you roll back a failed upgrade?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q398. How do you handle mixed controller versions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q399. How do you test upgrades in non-production?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **upgrade Kyverno CRDs, controllers, and policy APIs safely**. Identify the policy type, binding or scope, responsible...

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
helm -n kyverno history kyverno
kubectl get crd | grep kyverno.io
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q400. How do you create an enterprise Kyverno lifecycle calendar?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `release notes and compatibility → backup and CRD update → Helm controller rollout → webhook and policy tests → migration and observation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get crd | grep kyverno.io
helm -n kyverno history kyverno
```

**Risks:** image-only upgrades, CRD mismatch, legacy-policy changes, webhook failure, and unsafe rollback. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 21. OpenShift, Pod Security, SCCs, Routes, and Enterprise Platform Integration

## Q401. How does Kyverno operate on Red Hat OpenShift?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q402. How do OpenShift SecurityContextConstraints interact with Kyverno?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q403. How do Pod Security Admission and Kyverno policies complement each other?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q404. How do you validate OpenShift Routes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q405. How do you validate BuildConfig and DeploymentConfig resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q406. How do you validate OpenShift image streams?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q407. How do you restrict privileged service accounts on OpenShift?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q408. How do you generate NetworkPolicies for OpenShift projects?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q409. How do you enforce namespace or project labels?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q410. How do you validate Operator subscriptions and ClusterServiceVersions?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q411. How do you protect OpenShift GitOps resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. GitOps provides review and version history, but Kyverno mutation and generated resources require deliberate ownership and server-side diff configuration.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q412. How do you enforce Route TLS and host standards?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q413. How do you handle SCC mutation after Kyverno admission?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q414. How do you avoid conflict with OpenShift mutating webhooks?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a healthy Kyverno endpoint.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q415. How do you install Kyverno in disconnected OpenShift?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q416. How do you integrate Kyverno metrics with OpenShift monitoring?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q417. How do you troubleshoot OpenShift webhook connectivity?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get clusterpolicy,policy -A
oc get pods -n kyverno -o wide
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q418. How do you test policies against OpenShift resources?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **integrate Kyverno with OpenShift admission and platform APIs**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q419. How do you align Kyverno with Red Hat supportability?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q420. How do you create OpenShift Kyverno policy standards?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `OpenShift API request → SCC and other admission plugins plus Kyverno → Route, Build, Operator, or workload policy → cluster result`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
oc get pods -n kyverno -o wide
oc get clusterpolicy,policy -A
```

**Risks:** webhook ordering conflicts, SCC mutation differences, disconnected images, and unsupported platform policy assumptions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 22. Supply Chain, OCI Policies, Sigstore, SBOMs, and Artifact Governance

## Q421. How do you distribute Kyverno policies as OCI artifacts?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q422. How do you sign policy bundles?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q423. How do you verify policy artifact provenance?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q424. How do you manage policy catalogs?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q425. How do you govern community policy samples?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q426. How do you validate SBOM attestations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q427. How do you enforce vulnerability-scan attestations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q428. How do you require SLSA provenance attestations?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q429. How do you restrict build-system identities?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q430. How do you prevent unsigned base images?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q431. How do you enforce image digest usage?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q432. How do you manage multiple signature authorities?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q433. How do you rotate image-signing keys?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q434. How do you handle Sigstore transparency-log outages?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q435. How do you handle private Fulcio or Rekor services?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q436. How do you validate multi-architecture image indexes?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q437. How do you manage registry mirrors and rewrites?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
cosign verify <image>
kyverno apply policy.yaml --resource pod.yaml
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q438. How do you monitor policy and image verification supply-chain failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q439. How do you respond to a compromised signing key?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q440. How do you create enterprise artifact-governance standards?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **govern signed policies, images, SBOMs, and attestations**. Identify the policy type, binding or scope, responsible...

**Flow:** `trusted build and policy identity → OCI registry and signature → Kyverno verification → admission and audit → key rotation`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kyverno apply policy.yaml --resource pod.yaml
cosign verify <image>
```

**Risks:** compromised keys, transparency-log outage, weak identities, untrusted samples, and unsigned mirrors. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 23. Operations, Governance, Policy Lifecycle, and Production Readiness

## Q441. How do you define policy ownership and support boundaries?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q442. How do you classify policies by risk and enforcement level?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q443. How do you name and label policies consistently?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q444. How do you document policy intent and remediation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q445. How do you onboard application teams to Kyverno?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q446. How do you assess a policy's blast radius?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q447. How do you create a policy rollout plan?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q448. How do you create rollback criteria?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q449. How do you manage policy dependencies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q450. How do you manage exceptions and compensating controls?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q451. How do you review policies periodically?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q452. How do you identify obsolete policies?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q453. How do you decommission policies safely?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope, responsible controller,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q454. How do you perform policy drift reviews?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q455. How do you coordinate Kyverno with other admission controllers?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. The admission controller synchronously receives AdmissionReview requests and performs policy validation, mutation, image verification, and exception processing. Kyverno is a Kubernetes-native policy...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q456. How do you define change windows for Enforce policies?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q457. How do you create break-glass processes?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q458. How do you define incident escalation boundaries?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. During an incident, preserve policy versions, webhook configuration, controller logs, AdmissionReview context, events, reports, metrics, and external registry or Git evidence before...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get policyreports -A
kubectl get clusterpolicy,policy,policyexceptions -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q459. How do you create policy runbooks and service documentation?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **operate policy governance through documented lifecycle and ownership**. Identify the policy type, binding or scope,...

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q460. How do you create an enterprise Kyverno operating model?

### Answer

Treat this as a governance, admission-availability, and policy-lifecycle decision rather than only a YAML expression. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `policy proposal → risk review and tests → Audit deployment → remediation → Enforce → periodic review and retirement`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get clusterpolicy,policy,policyexceptions -A
kubectl get policyreports -A
```

**Risks:** ownerless policies, stale exceptions, missing remediation, uncontrolled blast radius, and weak break-glass process. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 24. Troubleshooting Webhooks, Policies, Controllers, Reports, and Registries

## Q461. How do you troubleshoot a Kyverno webhook timeout?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q462. How do you troubleshoot API-server connectivity to Kyverno?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q463. How do you troubleshoot webhook TLS certificate errors?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q464. How do you troubleshoot a policy stuck NotReady?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q465. How do you troubleshoot a CEL compilation failure?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. CEL-based Kyverno policies align with Kubernetes admission-policy APIs and are stable in Kyverno 1.18...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q466. How do you troubleshoot unexpected policy matches?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q467. How do you troubleshoot missing policy matches?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q468. How do you troubleshoot a mutation that is not applied?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q469. How do you troubleshoot generated resources that are missing?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q470. How do you troubleshoot mutate-existing failures?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q471. How do you troubleshoot background scans that do not run?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Background scanning periodically evaluates existing resources for supported validation and...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q472. How do you troubleshoot missing PolicyReports?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q473. How do you troubleshoot excessive report resources?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q474. How do you troubleshoot PolicyException not taking effect?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q475. How do you troubleshoot image-registry authentication?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q476. How do you troubleshoot image verification timeouts?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q477. How do you troubleshoot a cleanup policy that does not delete resources?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **diagnose Kyverno across API server, webhook,...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q478. How do you troubleshoot Kyverno after a Kubernetes upgrade?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
kubectl -n kyverno get pods -o wide
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q479. How do you collect a Kyverno diagnostic bundle?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q480. How do you create a Corporate/L3 Kyverno troubleshooting workflow?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and...

**Flow:** `user symptom → API-server webhook call → admission controller and policy status → background, report, cleanup, registry, or GitOps dependency → resolution`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl -n kyverno get pods -o wide
kubectl -n kyverno logs deploy/kyverno-admission-controller --since=30m
```

**Risks:** restarting before evidence, broad policy deletion, wrong layer diagnosis, and unvalidated recovery. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope, controller RBAC,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

# 25. Corporate L3 Kyverno Incident and Design Scenarios

## Q481. A new Enforce policy blocks all production deployments. How do you respond?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q482. A wildcard mutation changes system workloads unexpectedly. How do you contain it?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q483. Kyverno admission webhooks time out and the API server rejects writes. How do you recover?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q484. A PolicyException grants a much broader exemption than intended. How do you respond?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. PolicyException skips selected policy rules for narrowly matched resources. It is disabled by default and should be protected...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q485. An image-verification policy blocks emergency remediation images. How do you recover securely?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. Kyverno can verify signatures and attestations using Sigstore Cosign or Notary patterns and can resolve images to immutable...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q486. Registry latency causes deployment failures across the cluster. How do you stabilize admission?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q487. A generate rule creates duplicate RoleBindings in many namespaces. How do you recover?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and durable recovery**. Identify the policy type, binding or scope, responsible...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q488. A mutate-existing policy overwrites application-owned fields. How do you respond?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and durable recovery**....

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q489. A cleanup policy deletes resources earlier than expected. How do you investigate?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and durable recovery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q490. PolicyReports grow rapidly and increase etcd pressure. How do you stabilize the cluster?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and durable recovery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q491. An upgrade breaks legacy ClusterPolicy behavior. How do you recover?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. ClusterPolicy and CleanupPolicy are deprecated in Kyverno 1.18 and planned for removal in a later release, requiring tested migration rather than mechanical conversion.

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q492. A GitOps change creates Kyverno and Argo CD reconciliation loops. How do you resolve them?

### Answer

Implement this through a reviewed, tested, Audit-first, reversible procedure with a defined promotion and rollback path. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers. GitOps...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q493. Kyverno controllers are healthy but one namespace bypasses policy. How do you investigate?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q494. A malicious policy attempts to exfiltrate data through an HTTP context call. How do you contain it?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and durable recovery**. Identify the policy type, binding or scope, responsible controller, admission...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q495. A signing key is compromised after images were admitted. How do you investigate?

### Answer

Begin by isolating whether the failure is in API-server webhook selection, TLS and connectivity, policy readiness, rule evaluation, an external data source, or asynchronous controller processing. This topic belongs to the Corporate/L3 responsibility to **lead policy-engine incident containment and...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q496. Background scans show thousands of violations after a policy rollout. How do you prioritize remediation?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Background scanning periodically evaluates existing resources for supported validation and image-verification policies and is performed by the reports controller.

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q497. A webhook certificate expires during a production change window. How do you recover?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. A fail-closed admission webhook protects policy guarantees but can block matching API writes if the API server cannot reach a...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
kubectl get events -A --sort-by=.lastTimestamp
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q498. How do you lead a Kyverno production incident bridge?

### Answer

First contain policy or GitOps impact, preserve admission and controller evidence, protect security guarantees, and recover through a narrow canary or Audit-mode change. During an incident, preserve policy versions, webhook configuration, controller logs, AdmissionReview context, events, reports,...

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q499. What evidence belongs in a Kyverno root-cause analysis?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---

## Q500. What should a high-quality Corporate/L3 Kyverno runbook contain?

### Answer

Explain this from Kubernetes admission mechanics and Kyverno controller ownership perspectives. Kyverno is a Kubernetes-native policy engine which validates, mutates, generates, cleans up, and verifies images through admission controls and asynchronous controllers.

**Flow:** `declare scope → pause harmful policies or GitOps → preserve policy, webhook, controller, report, and API evidence → canary recovery → source correction → RCA`

**Practical approach**

1. Confirm versions, policy API, bindings, webhook scope, controller health, and the recent change.
2. Reproduce with `kyverno test` or `kyverno apply`; inspect readiness, logs, Events, reports, and UpdateRequests.
3. Test in Audit or a canary scope, measure API latency, and apply the smallest reversible correction.
4. Correct Git, Helm, exceptions, tests, dashboards, and the runbook before Enforce.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: kyverno-policy-change
  namespace: kyverno
data:
  owner: platform-security
  rollout: audit-canary-enforce
  validation: cli-tests-reports-and-api-latency
```

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations -o yaml > kyverno-webhooks.yaml
```

**Risks:** security bypass, cluster write outage, evidence destruction, source-of-truth mismatch, and ownerless corrective actions. Also verify webhook selectors and match conditions, `failurePolicy`, policy readiness, rule scope,...

**Verify:** Confirm the exact policy and binding revision, `kyverno test` or `kyverno apply` result, webhook configuration, controller readiness, logs, Events, PolicyReports, UpdateRequests, generated or mutated resource...

---
