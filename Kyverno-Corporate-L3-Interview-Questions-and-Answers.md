# Kyverno Corporate L3 — Interview Questions and Detailed Answers

> **Target audience:** Senior Kubernetes, OpenShift, DevSecOps, SRE, Platform Engineering, and Cloud Security professionals with approximately 8–12+ years of experience  
> **Interview level:** Corporate L3 / Senior Engineer / Technical Lead / Platform Security Engineer  
> **Document version:** 1.0  
> **Kyverno baseline:** v1.18.2, released July 10, 2026  
> **Important:** Kyverno APIs and features evolve. Validate API versions, Helm values, controller flags, and policy syntax against the version installed in the target cluster.

---

## Table of Contents

1. [Kyverno Fundamentals and Architecture](#1-kyverno-fundamentals-and-architecture)
2. [Installation, High Availability, and Upgrades](#2-installation-high-availability-and-upgrades)
3. [Policy Structure, Matching, and Evaluation](#3-policy-structure-matching-and-evaluation)
4. [Validate Policies](#4-validate-policies)
5. [Mutate Policies](#5-mutate-policies)
6. [Generate and Cleanup Policies](#6-generate-and-cleanup-policies)
7. [Image Verification and Software Supply Chain](#7-image-verification-and-software-supply-chain)
8. [Variables, Context, JMESPath, CEL, and External Data](#8-variables-context-jmespath-cel-and-external-data)
9. [Policy Reports, Background Scans, and Exceptions](#9-policy-reports-background-scans-and-exceptions)
10. [Security, RBAC, and Failure Design](#10-security-rbac-and-failure-design)
11. [Observability, Performance, and Scaling](#11-observability-performance-and-scaling)
12. [CLI Testing, GitOps, and CI/CD](#12-cli-testing-gitops-and-cicd)
13. [OpenShift and Enterprise Platform Scenarios](#13-openshift-and-enterprise-platform-scenarios)
14. [Corporate L3 Troubleshooting Scenarios](#14-corporate-l3-troubleshooting-scenarios)
15. [Rapid-Fire Commands and Diagnostic Checklist](#15-rapid-fire-commands-and-diagnostic-checklist)
16. [Official References](#16-official-references)

---

# 1. Kyverno Fundamentals and Architecture

## Q1. What is Kyverno?

### Answer

Kyverno is a cloud-native policy engine originally designed for Kubernetes. It allows platform and security teams to manage policy as Kubernetes-style declarative resources.

Kyverno can be used to:

- Validate resources and reject non-compliant admission requests.
- Mutate resources by adding or modifying fields.
- Generate resources from policy.
- Mutate existing resources through background processing.
- Verify container image signatures and attestations.
- Produce policy reports.
- Define cleanup policies for resource lifecycle management.
- Evaluate policy in CI/CD with the Kyverno CLI.
- Apply newer policy types to Kubernetes resources and, in some cases, generic JSON payloads.

A strong L3 answer should state that Kyverno is not only a validating webhook. It is a multi-controller policy platform with admission, background, reporting, cleanup, image verification, and policy testing capabilities.

---

## Q2. Why is Kyverno considered Kubernetes-native?

### Answer

Kyverno is considered Kubernetes-native because:

- Policies are represented as Kubernetes custom resources.
- Policies are written primarily in YAML.
- Matching commonly uses Kubernetes concepts such as kinds, namespaces, labels, subjects, operations, and selectors.
- Policy lifecycle can be managed with `kubectl`, Helm, GitOps, RBAC, audit logging, and Kubernetes APIs.
- Results can be stored as policy report resources.
- It can use Kubernetes API data and ConfigMaps as policy context.

This reduces the requirement for every platform administrator to learn a separate policy language for common Kubernetes governance tasks. However, advanced Kyverno policy authoring still requires good knowledge of JMESPath, CEL, admission behavior, Kubernetes object schemas, and failure handling.

---

## Q3. What problems does Kyverno solve in an enterprise Kubernetes platform?

### Answer

Common enterprise use cases include:

- Enforcing mandatory labels, annotations, and ownership metadata.
- Restricting privileged containers and unsafe security contexts.
- Enforcing approved registries.
- Blocking mutable image tags.
- Requiring signed images and attestations.
- Adding standard security defaults.
- Generating NetworkPolicies, ResourceQuotas, LimitRanges, or role bindings.
- Enforcing organizational naming standards.
- Preventing risky host-path, host-network, or host-PID usage.
- Restricting LoadBalancer or NodePort Services.
- Auditing existing resources for policy violations.
- Cleaning expired or temporary resources.
- Standardizing Pod security across Kubernetes and OpenShift.
- Shifting policy testing left into GitOps and CI pipelines.

Kyverno provides controls, but policy governance must also define ownership, rollout, exceptions, testing, monitoring, and emergency bypass procedures.

---

## Q4. Explain the major Kyverno controllers.

### Answer

A modern Kyverno installation commonly contains separate controller deployments.

### Admission controller

The admission controller processes Kubernetes admission requests. It evaluates matching validation, mutation, image verification, and other admission-time policies.

### Background controller

The background controller handles operations such as:

- Generate rules
- Synchronization of generated resources
- Mutate-existing behavior
- Reconciliation through UpdateRequest resources

### Reports controller

The reports controller reconciles intermediary admission and background scan results into PolicyReport and ClusterPolicyReport resources.

### Cleanup controller

The cleanup controller processes cleanup policy schedules and deletes resources that meet cleanup criteria.

Separating controller roles improves scaling, fault isolation, resource management, and operational visibility.

---

## Q5. Explain the Kyverno request flow during resource creation.

### Answer

A simplified admission path is:

1. A client submits a CREATE or UPDATE request to the Kubernetes API server.
2. The API server performs authentication and authorization.
3. Kubernetes admission processing invokes configured mutating and validating webhooks.
4. Kyverno receives an AdmissionReview request.
5. Kyverno identifies policies and rules matching the resource, operation, namespace, selectors, and exclusions.
6. Mutation rules may produce JSON patches.
7. Validation and image verification rules evaluate compliance.
8. Kyverno returns an AdmissionReview response.
9. The API server admits or rejects the resource.
10. Reporting components may later reconcile policy results into PolicyReport resources.

Admission is latency-sensitive. External API calls, registry access, complex loops, or overloaded controllers can directly affect API request latency.

---

## Q6. What is the difference between admission evaluation and background evaluation?

### Answer

### Admission evaluation

- Runs when a resource is submitted to the API server.
- Can block a CREATE or UPDATE request.
- Has access to AdmissionReview data such as operation, old object, user information, and request metadata.
- Must complete within the webhook timeout.
- Directly affects API availability and latency.

### Background evaluation

- Evaluates resources already stored in the cluster.
- Primarily records compliance results.
- Does not retroactively block an existing object.
- Cannot reliably use all admission-only fields because no live AdmissionReview exists.
- Is useful for continuous audit and drift visibility.

Do not assume that enabling `Enforce` will delete or block resources that already existed. Existing violations are normally reported unless another remediation mechanism is configured.

---

## Q7. What are the major Kyverno policy resource families?

### Answer

Depending on version and enabled features, a Kyverno environment may contain:

### Classic policy types

- `Policy`
- `ClusterPolicy`

These commonly contain rules of types such as validate, mutate, generate, and verifyImages.

### Newer specialized policy types

- `ValidatingPolicy`
- `MutatingPolicy`
- `ImageValidatingPolicy`
- `GeneratingPolicy`, where supported by the installed version
- Authorization-related policy types, where enabled and applicable

### Lifecycle and exception types

- `CleanupPolicy`
- `ClusterCleanupPolicy`
- `PolicyException`

Interview answers should clarify the installed API versions and avoid assuming every policy type exists in older Kyverno releases.

---

## Q8. What is the difference between `Policy` and `ClusterPolicy`?

### Answer

A `Policy` is namespaced. Its scope and behavior are associated with a namespace.

A `ClusterPolicy` is cluster-scoped and can match resources across namespaces and cluster-scoped resources according to the rule definition.

Use a namespaced policy when:

- A namespace owner should manage policy for that namespace.
- The control applies only to a specific tenant.
- RBAC delegation is required.

Use a cluster policy when:

- The standard is organization-wide.
- Cluster-scoped resources must be covered.
- Central platform governance is required.

Namespaced policy delegation must be controlled carefully to prevent tenants from weakening centrally required security controls.

---

## Q9. How is Kyverno different from Kubernetes ValidatingAdmissionPolicy?

### Answer

Kubernetes ValidatingAdmissionPolicy provides native CEL-based validation in the API server. Kyverno provides a broader policy-management platform.

Kyverno can offer:

- Validation
- Mutation
- Resource generation
- Image verification
- Policy reports
- Background scans
- Cleanup policies
- External data access
- Policy exceptions
- CLI testing
- Admission and non-admission policy execution patterns

Kubernetes native admission policies may reduce external webhook dependency for suitable validation use cases. Kyverno can coexist with or, in supported configurations, help manage or report on native validation policies.

An L3 design should select the simplest reliable mechanism for each requirement rather than forcing every control through one policy type.

---

## Q10. How is Kyverno different from OPA Gatekeeper?

### Answer

Both are policy engines, but they have different policy authoring and operational models.

Kyverno commonly emphasizes:

- Kubernetes-style YAML policy resources
- Mutation and generation
- Image verification
- Background reports
- Cleanup
- Built-in policy patterns
- JMESPath and newer CEL-based policy capabilities

Gatekeeper commonly uses:

- OPA/Rego
- ConstraintTemplates and Constraints
- OPA-based evaluation
- External data and mutation capabilities depending on configuration

A senior answer should avoid saying one is universally better. Evaluate:

- Team skill
- Required policy types
- Runtime performance
- Audit model
- Supply-chain requirements
- Multi-cluster governance
- Existing tooling
- Policy portability
- Exception and delegation models
- Upgrade and support strategy

---

# 2. Installation, High Availability, and Upgrades

## Q11. What is the recommended way to install Kyverno in production?

### Answer

The official Helm chart is generally the recommended production installation method because it exposes configuration for:

- Controller replicas
- Resource requests and limits
- Pod disruption budgets
- Webhook configuration
- RBAC
- Metrics
- tracing
- Service accounts
- security contexts
- node placement
- extra volumes
- platform-specific settings

Example repository configuration:

```bash
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm show values kyverno/kyverno > kyverno-values.yaml
```

Production installation should use a version-pinned chart and image set, not an unreviewed floating version.

---

## Q12. What should be validated before installing Kyverno?

### Answer

Validate:

- Kubernetes version compatibility.
- Kyverno and Helm chart version compatibility.
- Existing admission webhook inventory.
- API server webhook timeout budget.
- Network connectivity from API servers to Kyverno Services.
- Cluster DNS.
- certificate management.
- RBAC requirements.
- Pod Security Admission or OpenShift SCC constraints.
- Node availability zones.
- Resource capacity.
- Required CRDs.
- Proxy and registry access.
- Image signature and registry credential requirements.
- GitOps ownership.
- Disaster recovery and rollback plan.

Also test how the cluster behaves if Kyverno is unavailable under each configured `failurePolicy`.

---

## Q13. What is a typical high-availability Kyverno deployment?

### Answer

A common starting point is:

```bash
helm install kyverno kyverno/kyverno \
  --namespace kyverno \
  --create-namespace \
  --set admissionController.replicas=3 \
  --set backgroundController.replicas=2 \
  --set cleanupController.replicas=2 \
  --set reportsController.replicas=2
```

This is only a baseline. A complete HA design includes:

- Pod anti-affinity or topology spread constraints.
- Replicas distributed across nodes and zones.
- Pod disruption budgets.
- Resource requests sized from observed load.
- priority classes.
- Readiness and liveness probes.
- Reliable API server-to-webhook networking.
- Monitoring and alerting.
- Controlled maintenance windows.
- leader-election awareness for controllers where only one leader performs core work.

More replicas do not always mean proportional throughput for every controller.

---

## Q14. How do Kyverno controllers scale differently?

### Answer

### Admission controller

Multiple replicas can improve both availability and admission throughput, assuming load is distributed and the API server can reach all endpoints.

### Reports controller

Additional replicas primarily improve availability because leader election may make one replica responsible for active reconciliation. Vertical scaling and client rate tuning may be more important for throughput.

### Background controller

Multiple replicas improve availability, while worker-count and resource tuning can influence generation and mutate-existing processing.

### Cleanup controller

Multiple replicas can improve availability and concurrent cleanup throughput, but behavior depends on cleanup scheduling and job distribution.

Scaling must be based on controller-specific metrics and queue behavior.

---

## Q15. Which Kubernetes objects should be inspected after installation?

### Answer

```bash
kubectl get pods -n kyverno
kubectl get deploy -n kyverno
kubectl get svc -n kyverno
kubectl get endpoints,endpointslices -n kyverno
kubectl get validatingwebhookconfigurations,mutatingwebhookconfigurations
kubectl get crd | grep -i kyverno
kubectl get clusterrole,clusterrolebinding | grep kyverno
kubectl get events -n kyverno --sort-by=.lastTimestamp
```

Also inspect:

```bash
kubectl logs -n kyverno deploy/kyverno-admission-controller
kubectl logs -n kyverno deploy/kyverno-background-controller
kubectl logs -n kyverno deploy/kyverno-reports-controller
kubectl logs -n kyverno deploy/kyverno-cleanup-controller
```

Deployment names can vary with chart version and release name.

---

## Q16. How do you verify webhook health?

### Answer

Check:

```bash
kubectl get validatingwebhookconfiguration -o yaml
kubectl get mutatingwebhookconfiguration -o yaml
kubectl get svc,endpoints,endpointslices -n kyverno
kubectl get pods -n kyverno -o wide
```

Validate:

- Webhook Service name and namespace.
- Service port and target port.
- Non-empty endpoints.
- CA bundle.
- Rules and operations.
- namespace/object selectors.
- timeoutSeconds.
- failurePolicy.
- sideEffects.
- admissionReviewVersions.
- API server network reachability.
- TLS certificate validity and SANs.

Create a controlled test resource and observe API server and Kyverno logs.

---

## Q17. What is the safe process for upgrading Kyverno?

### Answer

1. Review Kyverno and Helm chart release notes.
2. Confirm supported Kubernetes versions.
3. Back up:
   - Policies
   - Exceptions
   - Cleanup policies
   - Helm values
   - Relevant ConfigMaps and Secrets
   - Webhook configuration
4. Test upgrade in a representative non-production cluster.
5. Run CLI tests against the new Kyverno version.
6. Check API deprecations and CRD conversion requirements.
7. Validate RBAC changes.
8. Validate webhook and certificate behavior.
9. Monitor admission latency and failures during rollout.
10. Confirm report, background, generate, cleanup, and image verification behavior.
11. Keep a documented rollback plan.

Do not downgrade blindly after CRD or stored-version changes.

---

## Q18. Why should policy testing be part of a Kyverno upgrade?

### Answer

A Kyverno upgrade can change:

- Schema validation.
- Variable handling.
- JMESPath behavior.
- CEL implementation.
- autogen behavior.
- image verification.
- webhook matching.
- reporting.
- policy exception processing.
- defaults.
- controller flags.
- metrics names or labels.

The same YAML should be tested with the target CLI version before the controller upgrade.

Example:

```bash
kyverno test ./policy-tests
kyverno apply ./policies \
  --resource ./resources \
  --policy-report
```

Unit tests should be complemented by in-cluster end-to-end tests.

---

## Q19. What is the purpose of PodDisruptionBudgets and topology spread for Kyverno?

### Answer

A PodDisruptionBudget limits voluntary disruptions so that maintenance operations do not remove too many Kyverno replicas simultaneously.

Topology spread or anti-affinity reduces the risk that all replicas run on the same node or failure zone.

These controls are especially important for the admission controller because loss of reachable admission endpoints can affect cluster writes when webhooks fail closed.

They do not protect against:

- Network partitions
- API server connectivity problems
- Invalid certificates
- policy-caused timeouts
- all nodes in a zone failing
- a bad rolling update

---

## Q20. What would you include in a Kyverno production readiness review?

### Answer

Include:

- Supported version matrix
- HA and topology
- Controller resource sizing
- Webhook failure policy
- Timeouts
- RBAC review
- NetworkPolicy
- TLS and certificate lifecycle
- policy ownership
- test coverage
- exception governance
- GitOps workflow
- monitoring and alerts
- backup and restore
- upgrade procedure
- emergency bypass
- audit retention
- load testing
- API server impact
- image registry failure behavior
- external service dependencies

---

# 3. Policy Structure, Matching, and Evaluation

## Q21. Explain the basic structure of a classic ClusterPolicy.

### Answer

Example:

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-team-label
spec:
  validationFailureAction: Audit
  background: true
  rules:
    - name: require-team-label
      match:
        any:
          - resources:
              kinds:
                - Deployment
      validate:
        failureAction: Audit
        message: "The label platform.example.com/team is required."
        pattern:
          metadata:
            labels:
              platform.example.com/team: "?*"
```

Major elements include:

- Policy metadata
- Policy-wide settings
- One or more rules
- Match and exclude criteria
- Optional context
- Optional preconditions
- A rule action such as validate, mutate, generate, or verifyImages

Version-specific fields should be validated against the installed CRD.

---

## Q22. How does `match` work?

### Answer

`match` selects resources and requests to which a rule may apply.

Common dimensions include:

- Resource kinds
- API groups and versions
- Namespaces
- Resource names
- Labels and selectors
- Admission operations
- Subjects, roles, or cluster roles in admission context
- `any` and `all` match blocks

The match block should first reduce the candidate set as efficiently and clearly as possible. More detailed field-level logic belongs in preconditions or policy expressions.

---

## Q23. What is the purpose of `exclude`?

### Answer

`exclude` removes selected resources from a rule after matching.

Use cases:

- Excluding system namespaces.
- Excluding a trusted controller ServiceAccount.
- Excluding a specific resource.
- Excluding break-glass workloads.
- Excluding resources with an approved label.

Avoid broad exclusions such as all resources created by cluster administrators unless the risk has been explicitly accepted. Attackers often target privileged identities; identity-based exclusions can create a bypass if overused.

---

## Q24. What is the difference between `any` and `all`?

### Answer

In conceptual terms:

- `any` succeeds when at least one child condition or selector matches.
- `all` succeeds only when every child condition or selector matches.

Example:

```yaml
match:
  any:
    - resources:
        kinds: ["Deployment"]
    - resources:
        kinds: ["StatefulSet"]
```

Use `any` for logical OR and `all` for logical AND.

Complex nested conditions should be tested because human interpretation errors are common, especially with negatives and exclusions.

---

## Q25. What are preconditions?

### Answer

Preconditions are rule-level gates evaluated after basic match/exclude selection and context loading.

They are useful when selection depends on:

- Fields inside the resource spec.
- Admission operation.
- Old versus new object values.
- Data from a ConfigMap.
- Kubernetes API query results.
- External service data.
- Calculated variables.

Example concept:

```yaml
preconditions:
  all:
    - key: "{{ request.object.spec.type }}"
      operator: Equals
      value: NodePort
```

If preconditions do not pass, the rule is skipped rather than failed.

---

## Q26. What is the difference between a skipped rule and a passed rule?

### Answer

A rule is typically skipped when:

- `match` does not select the resource.
- `exclude` removes the resource.
- Preconditions do not pass.
- Required evaluation does not apply to the request.

A rule passes when:

- It applies to the resource.
- Its validation conditions are satisfied.

This distinction matters in CLI tests, policy reports, metrics, and troubleshooting. A policy that always skips may provide no enforcement even though it shows no failures.

---

## Q27. What is autogen in Kyverno?

### Answer

Kyverno can automatically generate controller-level policy logic from suitable Pod policies so that Pod template controllers are covered.

Typical controllers include:

- Deployment
- StatefulSet
- DaemonSet
- Job
- CronJob
- ReplicaSet
- ReplicationController, depending on behavior and version

Autogen reduces duplication but can surprise authors because:

- Generated rule names appear in reports and exceptions.
- Variable paths are transformed for Pod templates.
- Not all Pod policies are eligible.
- Custom controllers may need configuration.
- Direct Pods and controller templates may behave differently.

Inspect the generated policy representation and test controller resources, not only standalone Pods.

---

## Q28. Why is rule ordering important?

### Answer

Rule ordering can matter when:

- Multiple mutation rules modify related fields.
- Image normalization or mutation affects later image verification.
- Context depends on fields introduced by mutation.
- Multiple policies interact through webhook ordering.
- A deny rule is expected to evaluate before an expensive external call.

Kubernetes admission executes mutating and validating phases according to webhook mechanics. Do not depend on undocumented ordering across independent webhook products.

Design policies to be deterministic and idempotent.

---

## Q29. What does idempotent policy behavior mean?

### Answer

An idempotent mutation produces the same final result when applied multiple times.

Good mutation:

- Adds a label only when absent.
- Merges a required capability drop.
- Adds a sidecar only when a unique marker is not present.

Bad mutation:

- Appends a duplicate list item every update.
- Changes a timestamp on every admission.
- Reorders arrays unpredictably.
- Causes another controller to reverse the change repeatedly.

Non-idempotent mutation can create update loops, API churn, and GitOps drift.

---

## Q30. How do you design policy names and rule names?

### Answer

Use stable, descriptive names because they appear in:

- Policy reports
- CLI test results
- Events
- Metrics
- Exceptions
- Audit evidence
- Alerting

Example naming:

```text
policy: restrict-privileged-containers
rule: block-privileged-container
rule: block-privileged-init-container
rule: block-privileged-ephemeral-container
```

Avoid renaming rules casually because PolicyExceptions and reporting integrations may reference them.

---

# 4. Validate Policies

## Q31. What are common validation methods in classic Kyverno policies?

### Answer

Classic validation capabilities commonly include:

- `pattern`
- `anyPattern`
- `deny`
- `foreach`
- Pod Security validation
- manifest/signature-related validation features depending on version

Use:

- `pattern` for declarative structural requirements.
- `anyPattern` when one of multiple accepted structures is valid.
- `deny` for expression-based rejection logic.
- `foreach` for validating every element in arrays.
- Pod Security controls for standardized Pod Security Standards evaluation.

---

## Q32. How does a validation pattern work?

### Answer

A validation pattern defines required structure or values.

Example:

```yaml
validate:
  message: "CPU and memory requests are required."
  pattern:
    spec:
      template:
        spec:
          containers:
            - resources:
                requests:
                  cpu: "?*"
                  memory: "?*"
```

Patterns can include anchors and wildcard semantics. Authors must understand how arrays and anchors are evaluated; a pattern that appears visually correct may validate only one matching element instead of every element.

Use tests with multiple containers and negative cases.

---

## Q33. What is `anyPattern`?

### Answer

`anyPattern` accepts a resource when any one listed pattern matches.

Example use cases:

- Require either an approved label or an approved annotation.
- Accept one of multiple security configuration models.
- Permit one of multiple approved volume types.

Use it carefully because each alternative becomes a compliance path. A weak alternative can unintentionally bypass a stronger one.

---

## Q34. When should a deny rule be used?

### Answer

Use a deny rule when failure logic is easier to express as a condition.

Example concept:

```yaml
validate:
  message: "NodePort Services are not permitted."
  deny:
    conditions:
      any:
        - key: "{{ request.object.spec.type }}"
          operator: Equals
          value: NodePort
```

Deny rules are useful for:

- Comparing old and new values.
- Multi-field logic.
- Numeric and duration comparisons.
- External context.
- Request identity.
- list membership.
- controlled update restrictions.

Document the exact condition under which denial occurs.

---

## Q35. How do you validate every container in a Pod?

### Answer

Use `foreach` or a carefully designed Pod Security/pattern rule that covers all relevant arrays:

- `spec.containers`
- `spec.initContainers`
- `spec.ephemeralContainers`

Conceptual example:

```yaml
validate:
  foreach:
    - list: "request.object.spec.containers"
      deny:
        conditions:
          any:
            - key: "{{ element.securityContext.privileged || `false` }}"
              operator: Equals
              value: true
```

A classic policy mistake is validating normal containers but forgetting init or ephemeral containers.

---

## Q36. How would you require approved image registries?

### Answer

A validation policy can check each image against approved prefixes.

Control all container arrays and normalize registry assumptions. An image such as `nginx:1.27` may resolve to a default registry, so policy and runtime interpretation must agree.

The enterprise design should define:

- Approved registries.
- Mirrors.
- Namespace or project-specific registries.
- digest requirements.
- exception process.
- handling of system components.
- registry failover.

Registry restriction does not prove image integrity. Combine it with image signature and attestation verification.

---

## Q37. How do you prevent use of the `latest` tag?

### Answer

Validate that:

- An explicit tag or digest exists.
- The tag is not `latest`.
- Optionally require immutable digest pinning.

Do not only search for the string `:latest`; untagged images commonly default to `latest`.

Image reference parsing must account for:

- Registry ports
- digests
- tags
- default registries
- default tags
- image mutations

Kyverno image variables and image verification features are preferable to fragile string splitting.

---

## Q38. How do you enforce immutable image references?

### Answer

Require an image digest such as:

```text
registry.example.com/app/backend@sha256:<digest>
```

Benefits:

- Reproducible deployment.
- Stronger promotion controls.
- Reduced risk of tag movement.
- Easier incident correlation.

Operational considerations:

- CI must resolve and record digests.
- Multi-architecture images use manifest-list digests.
- signature verification should validate the selected digest.
- Deployment tooling must not replace the digest with a mutable tag.
- promotion systems should use immutable artifacts.

---

## Q39. How do you enforce labels and annotations without blocking platform controllers incorrectly?

### Answer

Design the rule around the actual creation path:

- Match user-managed workloads.
- Exclude platform-generated resources only when necessary.
- Prefer validating top-level controllers rather than generated Pods when metadata originates in the controller template.
- Account for operator-created resources.
- Test direct Pods and controller-managed Pods separately.
- Use autogen intentionally.
- coordinate with GitOps and operators.

Do not exclude entire system namespaces without documenting the security impact.

---

## Q40. How do you enforce Pod Security Standards with Kyverno?

### Answer

Kyverno can enforce controls corresponding to Pod Security Standards such as Baseline or Restricted.

A mature approach:

1. Audit first.
2. Inventory violations.
3. Classify platform-required exceptions.
4. Add scoped PolicyExceptions.
5. Move low-risk namespaces to Enforce.
6. Monitor reports and admission rejections.
7. Periodically remove expired exceptions.

Kyverno may be used alongside Kubernetes Pod Security Admission or OpenShift SCC. Avoid conflicting controls with unclear ownership.

---

# 5. Mutate Policies

## Q41. What is mutation in Kyverno?

### Answer

Mutation changes a resource before it is persisted.

Common examples:

- Add labels or annotations.
- Set default resource requests.
- Add securityContext defaults.
- Add imagePullSecrets.
- Inject environment variables.
- Rewrite image registries.
- add sidecars or volumes.
- normalize configuration.

Mutation improves developer experience, but hidden mutation can make manifests differ from stored resources. Document mutations and expose them through CLI/GitOps testing.

---

## Q42. What is strategic merge patch mutation?

### Answer

Strategic merge patch uses Kubernetes-aware merge semantics for structured resources.

Example:

```yaml
mutate:
  patchStrategicMerge:
    metadata:
      labels:
        +(platform.example.com/managed): "true"
```

Advantages:

- Readable YAML.
- Natural object structure.
- Merge behavior for named list items.

Risks:

- Merge semantics vary by field and schema.
- CRDs may not support the same strategic merge behavior as built-in types.
- Anchors and list matching must be understood.
- Duplicate list entries must be prevented.

---

## Q43. What is JSON Patch 6902 mutation?

### Answer

JSON Patch expresses ordered operations such as add, replace, remove, test, copy, and move.

Example:

```yaml
mutate:
  patchesJson6902: |-
    - op: add
      path: /metadata/labels/platform.example.com~1managed
      value: "true"
```

Use JSON Pointer escaping:

- `/` becomes `~1`
- `~` becomes `~0`

JSON Patch is precise and suitable for arbitrary JSON structures, but it can be more fragile if parent paths do not exist or array positions change.

---

## Q44. How do you choose between strategic merge and JSON Patch?

### Answer

Choose strategic merge when:

- The resource has known Kubernetes merge semantics.
- Readability is important.
- You are merging map fields or named list elements.
- The patch should be declarative.

Choose JSON Patch when:

- Exact operation ordering is needed.
- The target is a CRD or generic JSON structure.
- Array positions or explicit removes are required.
- Strategic merge behavior is unsuitable.

Test both missing-field and existing-field cases.

---

## Q45. How do you add default resource requests safely?

### Answer

A safe policy should:

- Add values only when absent.
- Avoid overwriting application-defined requests.
- Treat CPU and memory independently.
- Cover init containers where appropriate.
- avoid giving every workload one unsuitable default.
- coordinate with LimitRange.
- test QoS-class impact.
- observe scheduling and capacity consequences.

Defaulting resources is not merely cosmetic; it changes scheduling, quotas, autoscaling signals, and potentially application performance.

---

## Q46. What is mutate-existing?

### Answer

Mutate-existing applies mutation logic to resources already present in the cluster through background-controller reconciliation.

Use cases:

- Add labels to existing namespaces.
- Update existing configuration after a trigger.
- Standardize previously created objects.
- Modify related target resources.

Risks:

- Large write volume.
- RBAC requirements.
- GitOps drift.
- update loops with other controllers.
- API throttling.
- unintended broad targeting.
- difficult rollback.

Use narrow matching, test on small scopes, and monitor UpdateRequest resources and controller queues.

---

## Q47. What is the difference between admission mutation and mutate-existing?

### Answer

Admission mutation:

- Modifies the incoming resource before storage.
- Is latency-sensitive.
- Runs on CREATE/UPDATE admission.
- Uses AdmissionReview context.

Mutate-existing:

- Changes resources already stored.
- Runs through background reconciliation.
- Requires permission to update target resources.
- may create many writes.
- can conflict with GitOps or operators.

They solve different problems and should be enabled independently where supported.

---

## Q48. How do you avoid mutation loops?

### Answer

Use:

- Conditional anchors or CEL conditions.
- Marker labels or annotations.
- Preconditions that detect desired state.
- Idempotent patch logic.
- Restricted target selectors.
- Clear ownership between Kyverno, GitOps, operators, and application controllers.

Monitor repeated updates:

```bash
kubectl get events -A --sort-by=.lastTimestamp
kubectl get ur -A
kubectl logs -n kyverno deploy/kyverno-background-controller
```

A loop often appears as high API write traffic, continuous reconciliation, and rising controller CPU.

---

## Q49. How does mutation interact with GitOps?

### Answer

If Git declares one state and Kyverno mutates another state, the GitOps controller may:

- Accept the mutation as server-side defaulting.
- Continuously report drift.
- Revert the mutation.
- enter a reconciliation loop.

Design options:

- Apply the same mutation in CI so Git contains the final form.
- Configure GitOps diff ignores only for explicitly approved fields.
- Use validation rather than mutation.
- Use Kubernetes-native defaults or LimitRanges.
- clearly assign field ownership.

Do not hide broad drift with large ignore rules.

---

## Q50. How do you test a mutation policy?

### Answer

Test:

- Field absent.
- Field already present.
- Conflicting existing value.
- Multiple containers.
- init and ephemeral containers.
- different API versions.
- controller templates.
- CRD schemas.
- repeated application.
- interaction with other mutations.

Use:

```bash
kyverno apply policy.yaml --resource resource.yaml
kyverno test ./tests
```

Then perform in-cluster admission tests because CLI execution does not reproduce every Kubernetes admission mutation and defaulting behavior.

---

# 6. Generate and Cleanup Policies

## Q51. What is a generate rule?

### Answer

A generate rule creates or clones resources when a triggering resource matches policy.

Use cases:

- Generate a default NetworkPolicy when a Namespace is created.
- Generate ResourceQuota and LimitRange.
- Clone a standard Secret or ConfigMap.
- create tenant role bindings.
- provision baseline monitoring objects.
- generate namespace-specific configuration.

Generation is asynchronous and typically reconciled by the background controller through UpdateRequest resources.

---

## Q52. What is the difference between data generation and clone generation?

### Answer

### Data generation

The policy contains the desired generated resource definition.

### Clone generation

The generated resource is copied from an existing source resource.

Clone-based designs centralize source configuration but create dependencies on:

- Source existence.
- source RBAC.
- namespace boundaries.
- secret handling.
- synchronization semantics.
- deletion behavior.

Never clone secrets broadly without explicit authorization and data-classification review.

---

## Q53. What does synchronization mean for generated resources?

### Answer

With synchronization enabled, Kyverno attempts to keep the generated resource aligned with the policy or source.

This may:

- Recreate a deleted generated resource.
- restore fields modified by users.
- update clones when the source changes, depending on rule behavior.
- conflict with another reconciler.

Before enabling synchronization, define who owns the generated resource and which system is authoritative.

---

## Q54. What are UpdateRequest resources?

### Answer

UpdateRequests are intermediary Kyverno resources used by the background controller to process generate and mutate-existing operations.

Inspect them:

```bash
kubectl get updaterequests -A
kubectl get ur -A
kubectl describe ur <name> -n kyverno
```

Depending on version and configuration, they can show:

- Pending
- completed
- failed
- policy and resource references
- messages or status details

A backlog indicates controller, RBAC, API throttling, target, or policy problems.

---

## Q55. What RBAC is required for generate policies?

### Answer

The background controller needs permission to:

- Read triggering resources.
- Read source resources for clone operations.
- create/update/delete generated targets as required.
- manage UpdateRequests.
- access target namespaces.

Kyverno should not receive cluster-admin merely to make a policy work. Add scoped permissions through supported chart/RBAC extension mechanisms and verify with:

```bash
kubectl auth can-i create networkpolicies \
  --as=system:serviceaccount:kyverno:<background-service-account> \
  -n target-namespace
```

---

## Q56. What is a CleanupPolicy?

### Answer

A CleanupPolicy deletes resources matching defined criteria on a schedule.

There are commonly:

- Namespaced `CleanupPolicy`
- Cluster-scoped `ClusterCleanupPolicy`

Use cases:

- Remove expired test resources.
- delete completed Jobs after a retention period.
- clean preview environments.
- remove temporary namespaces under strict controls.
- clean stale leases or generated resources.

Cleanup is destructive and must include narrow selection, dry-run/testing strategy, exclusions, audit, and recovery planning.

---

## Q57. How would you design a safe cleanup policy?

### Answer

Include:

- Exact kinds.
- namespace scope.
- labels controlled by a trusted workflow.
- age or expiration logic.
- protected-resource exclusions.
- schedule reviewed for timezone and load.
- least-privilege delete RBAC.
- audit events and alerts.
- backup requirements.
- test resources.
- emergency disable procedure.

Do not delete based only on a user-editable label such as `temporary=true` in a multi-tenant cluster.

---

## Q58. Generate policy versus GitOps templating: when would you choose each?

### Answer

Choose Kyverno generate when:

- Creation is event-driven by cluster resources.
- Every namespace should automatically receive a baseline.
- Runtime reconciliation is desired.
- Policy and generated object relationship is useful.

Choose GitOps templating when:

- Desired resources should be explicit in Git.
- change review and environment promotion are primary.
- deterministic deployment order is required.
- runtime controller ownership is undesirable.

Hybrid use is possible, but ownership must be unambiguous.

---

# 7. Image Verification and Software Supply Chain

## Q59. What is `verifyImages`?

### Answer

`verifyImages` is a Kyverno policy capability used to verify container image signatures and attestations before workloads are admitted.

It can support designs based on:

- Cosign signatures.
- key-based signing.
- keyless identities.
- in-toto attestations.
- digest mutation or verification.
- registry credentials.
- attestor sets.

It protects supply-chain trust, not application runtime behavior.

---

## Q60. What is an ImageValidatingPolicy?

### Answer

`ImageValidatingPolicy` is a newer specialized Kyverno policy type for image signature and attestation validation.

It can define:

- Resource match constraints.
- image reference matching.
- attestors.
- validation actions.
- failure policy.
- signature verification.
- attestation verification.
- CEL-based expressions and payload processing.

Use the policy type supported by the installed version and migration strategy. Do not assume classic `verifyImages` syntax and ImageValidatingPolicy syntax are interchangeable.

---

## Q61. What is the difference between key-based and keyless signing?

### Answer

### Key-based signing

Verification uses a configured public key.

Advantages:

- Simple trust root.
- can work in controlled offline models.

Risks:

- Private-key protection.
- rotation.
- distribution.
- revocation.
- attribution if many users share one key.

### Keyless signing

Verification commonly trusts an identity issued through an OIDC-based workflow and transparency infrastructure.

Advantages:

- Short-lived signing credentials.
- identity can be tied to CI workflow or user.
- reduced long-lived private-key distribution.

Risks:

- identity expression must be strict.
- issuer and subject matching.
- dependency on identity and transparency services.
- workflow compromise.

---

## Q62. What is an attestation?

### Answer

An attestation is a signed statement about an artifact.

Examples:

- Vulnerability scan result.
- SBOM.
- build provenance.
- source repository and commit.
- test result.
- policy compliance.
- builder identity.

Signature verification proves who signed the attestation and that it was not modified. Policy must still validate the attestation content.

A signed statement saying “scan completed” is not enough if the policy does not check scan age, scanner identity, severity threshold, and artifact digest.

---

## Q63. How would you enforce vulnerability scan attestations?

### Answer

Require:

- Trusted attestation signer.
- Artifact digest binding.
- approved predicate type.
- scanner identity.
- maximum scan age.
- critical/high vulnerability threshold.
- exception workflow.
- handling for unavailable scanner or registry.
- revocation strategy.

Test malicious cases:

- Attestation for another digest.
- unsigned payload.
- trusted signer with wrong predicate.
- old scan.
- missing fields.
- malformed severity data.
- keyless identity regex too broad.

---

## Q64. Why should image references be mutated to digests?

### Answer

Tags are mutable. Resolving an image to its digest:

- binds policy verification to immutable content.
- prevents a tag from changing after admission.
- improves auditability.
- supports reproducible rollback.
- correlates running workload to scan and signature evidence.

Consider registry availability and caching because digest resolution may require external access during admission.

---

## Q65. What happens if the registry is unavailable during image verification?

### Answer

The outcome depends on:

- Policy failure behavior.
- Webhook failurePolicy.
- timeout.
- cached data.
- whether verification requires registry access.
- credential availability.
- image reference form.
- controller health.

A fail-closed design protects integrity but can block deployments during a registry outage. A fail-open design improves availability but creates an enforcement gap.

Make the decision by workload criticality and threat model; do not leave it as an accidental default.

---

## Q66. How are private registry credentials handled?

### Answer

Kyverno may need credentials to fetch signatures, attestations, manifests, or image metadata.

Enterprise controls:

- Least-privilege pull credentials.
- dedicated ServiceAccount or secret.
- namespaced credentials where supported.
- pod imagePullSecrets where supported.
- short-lived tokens.
- no credentials in policy YAML.
- network restrictions.
- registry audit logging.
- secret rotation.
- separation between production and non-production registries.

Test image verification after credential rotation.

---

## Q67. What is a dangerous keyless identity pattern?

### Answer

A broad regular expression can trust unintended workflows.

Risky concept:

```text
subjectRegExp: ".*myorg.*"
```

Safer trust defines:

- Exact issuer.
- exact organization.
- repository.
- branch or tag restrictions.
- workflow file.
- environment.
- event type where available.

Review identity claims captured in real signatures before writing the policy.

---

## Q68. How do you roll out image verification safely?

### Answer

1. Inventory image registries and signing coverage.
2. Start in Audit.
3. sign platform and application images.
4. test private registry access.
5. identify third-party image strategy.
6. define exception owners and expiry.
7. monitor latency and registry failures.
8. enforce in non-production.
9. enforce by namespace or workload tier.
10. add CI pre-verification.
11. protect signer identities and workflows.
12. periodically audit unsigned running images.

---

# 8. Variables, Context, JMESPath, CEL, and External Data

## Q69. What variables are available in classic Kyverno policies?

### Answer

Common variables can include:

- `request.object`
- `request.oldObject`
- `request.operation`
- request user information
- namespace labels or metadata
- image information
- values loaded into rule context
- foreach element variables
- service or API call results

Availability depends on:

- Admission versus background execution.
- resource type.
- policy type.
- Kyverno version.
- rule context.

Do not use admission-only variables in a background-enabled policy without designing for missing data.

---

## Q70. What is the context block?

### Answer

The context block loads or calculates data used by a rule.

Possible sources include:

- Variables.
- ConfigMaps.
- Kubernetes API calls.
- Service/API calls.
- image registry data.
- GlobalContextEntry.
- nested expressions.

Context enables dynamic policy but introduces dependencies and latency. Keep admission context calls bounded, secured, and observable.

---

## Q71. What is JMESPath used for in Kyverno?

### Answer

JMESPath is used to query and transform JSON data in many classic Kyverno policy features.

Examples include:

- Select container images.
- count array elements.
- filter lists.
- read nested fields.
- compare values.
- build derived data.

Use the Kyverno CLI `jp` command where appropriate:

```bash
kyverno jp query 'spec.containers[].image' resource.yaml
```

Test null and missing-field behavior. Expressions that work for one resource shape may fail or return empty values for another.

---

## Q72. What is CEL used for in Kyverno?

### Answer

CEL is used by newer Kyverno policy types and Kubernetes-native policy integrations for expression-based matching, validation, mutation, and exception logic.

Benefits:

- Strong expression model.
- alignment with Kubernetes admission policy evolution.
- reusable patterns for JSON and Kubernetes objects.
- clear boolean validation logic.

Migration requires training because CEL semantics differ from JMESPath and classic Kyverno patterns.

---

## Q73. JMESPath versus CEL: how do you explain the difference?

### Answer

JMESPath is primarily a JSON query and transformation language. Kyverno extends it with custom functions.

CEL is an expression language designed for safe, bounded evaluation and is used widely in Kubernetes APIs.

In Kyverno:

- Classic ClusterPolicy rules often use JMESPath variables and conditions.
- Newer specialized policy types may use CEL.
- Both can coexist.
- The correct language depends on policy type and version.

Do not mechanically translate syntax; translate the intended logic and test edge cases.

---

## Q74. How can a ConfigMap be used as external policy data?

### Answer

A policy can load ConfigMap data into context and compare the incoming resource to centrally managed values.

Use cases:

- Approved registries.
- allowed storage classes.
- environment-specific limits.
- team mappings.
- maintenance windows.

Risks:

- ConfigMap availability.
- change control.
- data parsing.
- namespace access.
- cache freshness.
- unauthorized modification.

Protect policy-data ConfigMaps with RBAC and GitOps.

---

## Q75. How can Kyverno query the Kubernetes API?

### Answer

An `apiCall` context can retrieve Kubernetes objects or collections.

Use cases:

- Check namespace attributes.
- look up related resources.
- enforce uniqueness.
- validate references.
- count existing objects.

Admission-time API calls can increase latency and API load. Avoid list-all queries across large clusters. Use field/label selectors and precomputed context where possible.

---

## Q76. What are the risks of external HTTP service calls?

### Answer

Risks include:

- Admission latency.
- timeouts.
- service outage.
- SSRF.
- access to sensitive network endpoints.
- token leakage.
- inconsistent results.
- dependency cycles.
- unbounded response size.
- weak TLS validation.
- policy bypass if failure opens.

Kyverno v1.18 added stronger controls around secure HTTP calls. Still use explicit allowlists/blocklists, scoped tokens, TLS, rate limits, and stable services.

---

## Q77. What is GlobalContextEntry?

### Answer

A GlobalContextEntry allows reusable data to be loaded and made available to policies, reducing repeated direct lookups.

Potential benefits:

- Lower admission latency.
- central reusable context.
- fewer API calls.
- consistent policy data.

Operational concerns:

- Refresh behavior.
- stale data.
- RBAC.
- error handling.
- memory usage.
- ownership.
- version support.

Use it for data appropriate to caching, not rapidly changing authorization decisions unless staleness is acceptable.

---

## Q78. How do you handle missing variables safely?

### Answer

Use:

- Defaults.
- null-safe expressions.
- preconditions.
- explicit existence checks.
- tests for missing nested maps and arrays.
- request-operation guards.

Example concept:

```yaml
key: "{{ request.object.metadata.labels.team || '' }}"
```

Never assume optional Kubernetes fields exist. A missing field should produce a deliberate pass, skip, or fail—not an accidental policy error.

---

# 9. Policy Reports, Background Scans, and Exceptions

## Q79. What are PolicyReport and ClusterPolicyReport?

### Answer

PolicyReport resources record policy evaluation results.

- `PolicyReport` is namespaced and reports on namespaced resources.
- `ClusterPolicyReport` covers cluster-scoped resources.

Results can include states such as:

- pass
- fail
- warn
- error
- skip

Reports support:

- Compliance dashboards.
- audit workflows.
- alerting.
- trend analysis.
- exception visibility.
- remediation prioritization.

Reports are not immutable audit logs. Export important compliance evidence to a durable external system.

---

## Q80. How do background scans work?

### Answer

Background scanning periodically evaluates existing resources against supported policy rules.

Key points:

- It identifies current non-compliance.
- It updates policy reports.
- It does not block an object that already exists.
- It may not have admission identity or operation data.
- Large scans consume API, controller, and report resources.
- Namespace and resource filtering behavior must be understood.

Use background scans for audit and drift visibility, then choose separate remediation mechanisms.

---

## Q81. Why might a policy work at admission but fail or skip in background?

### Answer

Possible causes:

- Use of `request.userInfo`.
- use of request roles or subjects.
- operation-specific conditions.
- dependence on `request.oldObject`.
- external context unavailable in background.
- resource filters.
- policy setting disables background.
- policy type does not support the same background behavior.
- exception behavior differs.

Design distinct rules when admission and continuous compliance require different context.

---

## Q82. What is a PolicyException?

### Answer

PolicyException is a separate resource used to exempt selected resources from selected policies or rules.

Advantages over editing the policy:

- Separation of policy and exception lifecycle.
- namespace-scoped exception ownership.
- easier expiry and audit.
- avoids broad policy exclusions.
- can support newer CEL-based policy types.

PolicyExceptions are disabled by default in some configurations and must be explicitly enabled.

---

## Q83. How do you govern PolicyExceptions?

### Answer

Require:

- Business justification.
- risk owner.
- exact policy and rule.
- exact resource scope.
- expiration date.
- compensating controls.
- approval workflow.
- periodic review.
- automated expiry alerts.
- restricted RBAC.
- policy validation of exception resources.

Avoid wildcard policy/rule exceptions unless a formal break-glass process authorizes them.

---

## Q84. What is the difference between `exclude` and PolicyException?

### Answer

`exclude` is embedded in the policy and is managed with the policy lifecycle.

PolicyException is a separate resource and can be managed independently.

Use `exclude` for permanent architecture-level exclusions, such as a documented system component.

Use PolicyException for temporary or delegated exceptions requiring independent approval and expiry.

---

## Q85. Why can report resources accumulate?

### Answer

Possible causes:

- Reports controller crash or throttling.
- slow reconciliation.
- excessive admission volume.
- too many policy results.
- API client QPS/burst too low.
- report cleanup jobs not running.
- RBAC failures.
- API server pressure.

Inspect:

```bash
kubectl get admissionreports -A
kubectl get backgroundscanreports -A
kubectl get policyreports -A
kubectl logs -n kyverno deploy/kyverno-reports-controller
```

Tune only after confirming the bottleneck.

---

## Q86. How do you operationalize policy reports?

### Answer

A mature workflow:

1. Export reports to monitoring or compliance systems.
2. Assign ownership by namespace/team labels.
3. alert on new high-severity violations.
4. measure pass/fail trends.
5. identify stale exceptions.
6. create remediation tickets.
7. separate policy engine errors from resource failures.
8. retain external historical evidence.
9. define SLOs for remediation.
10. avoid excessive high-cardinality metric labels.

---

# 10. Security, RBAC, and Failure Design

## Q87. Explain `failurePolicy: Fail` versus `Ignore`.

### Answer

### Fail

If the webhook cannot process the request because of an error or timeout, admission is rejected.

Advantages:

- Strong enforcement.
- prevents bypass during controller outage.

Risks:

- Kyverno or network failure can block cluster writes.

### Ignore

The API server continues when the webhook fails.

Advantages:

- Higher availability.

Risks:

- Policy enforcement gap.

This is a security-versus-availability decision. It should be made per workload and policy criticality with tested emergency procedures.

---

## Q88. What is the difference between policy failure action and webhook failurePolicy?

### Answer

Policy failure action controls what happens when a policy successfully evaluates a resource as non-compliant:

- Enforce: reject.
- Audit: allow and report.

Webhook `failurePolicy` controls what Kubernetes does when the webhook itself fails, times out, or returns certain unrecognized errors:

- Fail: reject.
- Ignore: continue.

These are independent decisions and are often confused in interviews.

---

## Q89. How do you apply least privilege to Kyverno?

### Answer

- Start with chart-provided RBAC.
- Add only permissions required by actual policies.
- separate admission, background, reports, and cleanup permissions.
- avoid cluster-admin.
- use namespaced permissions where possible.
- protect ServiceAccounts and tokens.
- use projected short-lived tokens where supported.
- audit denied actions.
- review permissions after policy removal.

Check effective access:

```bash
kubectl auth can-i --list \
  --as=system:serviceaccount:kyverno:<service-account>
```

---

## Q90. Why can a generate policy create a privilege-escalation path?

### Answer

If an untrusted user can create a trigger resource that causes the background controller to generate a privileged object, the user may indirectly use Kyverno's stronger permissions.

Example risk:

- Tenant creates a specially labeled ConfigMap.
- Generate policy creates a ClusterRoleBinding.
- Background controller has permission to bind cluster-admin.

Prevent this with:

- Trusted trigger kinds and namespaces.
- immutable or protected labels.
- strict preconditions.
- RBAC and admission controls on triggers.
- narrow generated-resource permissions.
- threat modeling of confused-deputy scenarios.

---

## Q91. How do you secure Kyverno network access?

### Answer

Use NetworkPolicies or platform controls to permit only required traffic:

- API server to admission webhook.
- Kyverno controllers to Kubernetes API.
- DNS.
- registry endpoints.
- OpenTelemetry collector.
- approved external policy-data services.

Block unnecessary pod-to-webhook access and unauthorized egress. Validate cluster-specific API server source paths before enforcing restrictive ingress.

---

## Q92. How do you protect Kyverno policy resources?

### Answer

- Restrict create/update/delete RBAC.
- protect Git repositories and branches.
- require review from platform/security owners.
- test policies before merge.
- use admission policy to validate policies and exceptions.
- audit changes.
- use signed Git commits or release artifacts where required.
- separate author and approver for critical policies.
- back up policies and Helm values.
- monitor policy deletion and Enforce-to-Audit changes.

---

# 11. Observability, Performance, and Scaling

## Q93. What should be monitored in Kyverno?

### Answer

Monitor:

- Admission request count.
- admission latency.
- policy and rule execution latency.
- webhook errors.
- controller restarts.
- CPU and memory.
- work queue depth.
- API client throttling.
- UpdateRequest backlog.
- report backlog.
- cleanup failures.
- image verification latency and failures.
- policy changes.
- certificate expiration.
- API server webhook rejection metrics.

Use both Kyverno and Kubernetes control-plane telemetry.

---

## Q94. How do you troubleshoot high admission latency?

### Answer

1. Confirm API server webhook latency.
2. inspect Kyverno admission metrics.
3. identify slow policies and rules.
4. inspect external API or registry calls.
5. check CPU throttling and memory pressure.
6. check network and DNS.
7. inspect webhook timeout.
8. review large foreach loops.
9. reduce broad match scopes.
10. test scaling admission replicas.
11. inspect API client throttling.
12. use distributed tracing.

Do not simply increase timeout; that may make API stalls longer.

---

## Q95. What causes policy execution to become expensive?

### Answer

- Broad matching across many kinds.
- list-all Kubernetes API calls.
- external HTTP calls.
- registry lookups.
- large nested foreach loops.
- complex expressions.
- high-cardinality context.
- excessive policies matching every request.
- report generation volume.
- insufficient CPU.
- repeated image verification without effective caching.
- mutation patches over very large objects.

Optimize match conditions before expensive context loading.

---

## Q96. How do you use tracing with Kyverno?

### Answer

Kyverno supports OpenTelemetry-based tracing in supported versions.

Tracing can show:

- Admission request spans.
- policy execution.
- rule execution.
- external calls.
- controller operations.
- latency breakdown.

Typical backends include Jaeger, Grafana Tempo, and other OpenTelemetry-compatible systems.

Protect trace data because it can include resource names, policy names, identities, and operational metadata.

---

## Q97. How do you scale admission throughput?

### Answer

- Increase admission replicas.
- ensure endpoints are distributed.
- allocate adequate CPU.
- remove CPU throttling.
- tune policy match scope.
- reduce external calls.
- use cached/global context when appropriate.
- reduce unnecessary webhook rules.
- tune API client QPS/burst carefully.
- load-test.
- monitor p95/p99 latency and rejection rate.
- use autoscaling only with reliable signals and minimum replicas.

Memory-based autoscaling support may be available in newer chart versions, but scaling policy must be validated for actual workload.

---

## Q98. What is the impact of too many PolicyReports?

### Answer

Potential impact:

- etcd storage.
- API server list/watch load.
- reports controller CPU and memory.
- monitoring cardinality.
- slower compliance queries.
- noisy events.
- backup size.

Use report lifecycle controls, external aggregation, scoped policies, and appropriate reporting settings. Do not disable reporting globally without understanding compliance consequences.

---

## Q99. How would you define Kyverno SLOs?

### Answer

Example SLO dimensions:

- Admission availability.
- p95 and p99 admission latency.
- maximum policy engine error rate.
- maximum UpdateRequest age.
- maximum report reconciliation delay.
- controller availability across zones.
- image verification success for healthy registries.
- exception expiry compliance.
- policy test pass rate.
- upgrade rollback readiness.

SLOs should distinguish non-compliant resource rejection from Kyverno system failure.

---

## Q100. What distinguishes a Corporate L3 Kyverno engineer?

### Answer

An L3 engineer can:

- Explain admission and background architecture.
- write safe validate, mutate, generate, cleanup, and image policies.
- understand classic JMESPath and newer CEL policy models.
- design HA and failure behavior.
- troubleshoot webhook, RBAC, TLS, network, registry, and report issues.
- govern PolicyExceptions.
- protect against confused-deputy privilege escalation.
- optimize performance.
- integrate policy testing into GitOps.
- plan upgrades and rollback.
- communicate risk, blast radius, and compensating controls.
- correlate policy decisions with cluster and supply-chain evidence.

A senior answer always includes:

- Why
- risk
- scope
- validation
- rollback
- monitoring
- governance
- prevention

---

# 12. CLI Testing, GitOps, and CI/CD

## Q101. What is the Kyverno CLI?

### Answer

The Kyverno CLI allows policy evaluation and testing outside the cluster.

Common commands:

```bash
kyverno version
kyverno apply
kyverno test
kyverno jp
kyverno create test
```

Use cases:

- Pre-commit validation.
- CI policy checks.
- unit tests.
- policy upgrade simulation.
- local troubleshooting.
- policy report generation from manifests.
- JMESPath testing.

Use the CLI version that matches or is explicitly tested against the cluster version.

---

## Q102. What is the difference between `kyverno apply` and `kyverno test`?

### Answer

`kyverno apply` evaluates policies against resources and shows results or mutations.

```bash
kyverno apply ./policies --resource ./resources
```

`kyverno test` compares actual behavior with expected results declared in test files.

```bash
kyverno test ./tests
```

Use apply for exploration and pipeline compliance scans. Use test for repeatable regression tests.

---

## Q103. What should a policy test suite include?

### Answer

For every rule include:

- Positive case.
- negative case.
- skip case.
- missing-field case.
- multiple-array-element case.
- exception case.
- background-compatible case.
- direct Pod and controller case.
- old/new object update case where applicable.
- mutation idempotency.
- external-data failure case.
- version-upgrade case.

A test suite containing only one expected failure is not sufficient.

---

## Q104. What are limitations of CLI testing?

### Answer

CLI testing may not reproduce:

- Full Kubernetes defaulting.
- all mutating admission interactions.
- every webhook ordering effect.
- live RBAC.
- network failures.
- registry behavior unless enabled.
- API server schema behavior.
- operator reconciliation.
- performance under concurrent load.
- cluster-specific admission plugins.

Use CLI tests plus in-cluster end-to-end tests such as Chainsaw or another approved framework.

---

## Q105. How do you integrate Kyverno into GitOps?

### Answer

A mature workflow:

1. Store policies in Git.
2. pin Kyverno and CLI versions.
3. lint YAML and validate schemas.
4. run `kyverno test`.
5. run `kyverno apply` against application manifests.
6. review rendered Helm/Kustomize output.
7. deploy Audit first.
8. observe reports.
9. promote Enforce through environments.
10. manage exceptions as reviewed Git resources.
11. alert on out-of-band policy changes.

---

## Q106. How should policies be organized in Git?

### Answer

Example:

```text
kyverno/
├── base/
│   ├── security/
│   ├── reliability/
│   ├── cost/
│   └── supply-chain/
├── overlays/
│   ├── dev/
│   ├── staging/
│   └── production/
├── exceptions/
├── tests/
├── values/
└── docs/
```

Include:

- Owner.
- severity.
- rollout state.
- rationale.
- exception guidance.
- remediation.
- test cases.
- version compatibility.

Avoid copying nearly identical policies per cluster without a maintainable overlay strategy.

---

## Q107. How do you promote Audit to Enforce?

### Answer

Use measurable gates:

- Policy deployed in Audit for a defined period.
- Existing violation inventory complete.
- false positives resolved.
- application owners notified.
- exceptions approved and time-bound.
- CI prevents new violations.
- dashboards and alerts ready.
- break-glass tested.
- non-production Enforce successful.
- change approval recorded.

Switching directly to Enforce across all namespaces is a common production outage cause.

---

## Q108. How do you prevent policy bypass through GitOps ordering?

### Answer

- Install Kyverno and wait for readiness before protected workloads.
- apply CRDs before custom resources.
- sequence policy dependencies.
- avoid disabling webhooks during application synchronization.
- use sync waves or health checks.
- protect policy namespace.
- detect deleted policies.
- ensure failure behavior is intentional during upgrades.
- do not deploy unsigned workloads before image policies become active.

---

# 13. OpenShift and Enterprise Platform Scenarios

## Q109. How does Kyverno interact with OpenShift SCC?

### Answer

OpenShift Security Context Constraints participate in admission and may default or reject security fields.

Kyverno can:

- validate intended security posture.
- mutate defaults.
- enforce organization-specific rules.
- report compliance.

Potential issues:

- Kyverno validates before or after fields are defaulted differently than expected.
- SCC allows a setting Kyverno blocks.
- Kyverno adds a field incompatible with SCC.
- CLI tests do not reproduce SCC mutation.
- ServiceAccounts receive SCC permissions indirectly.

Always test on OpenShift, not only upstream Kubernetes.

---

## Q110. Kyverno policy passes in Kubernetes but fails on OpenShift. What do you check?

### Answer

Check:

- OpenShift API kinds and admission plugins.
- SCC mutation.
- Route, BuildConfig, ImageStream, and DeploymentConfig schemas.
- OpenShift namespaces and operators.
- default UID ranges.
- SELinux options.
- image registry references.
- webhook networking.
- proxy and custom CA.
- platform-generated metadata.
- API server audit logs.
- resource conversion/defaulting.

---

## Q111. How would you enforce OpenShift project governance?

### Answer

Possible policies:

- Require owner and cost-center labels on Namespace/Project.
- generate ResourceQuota and LimitRange.
- generate default NetworkPolicy.
- restrict Route hostnames.
- restrict privileged SCC use.
- require approved image registries.
- validate BuildConfig source.
- validate ServiceAccount annotations.
- enforce node selector/toleration policy.
- prevent unauthorized LoadBalancer Services.

Coordinate with OpenShift operators and cluster policy because generated resources may be reconciled by platform components.

---

## Q112. How do you handle operator-managed resources?

### Answer

Operators continuously reconcile resources. Kyverno mutation or generation can conflict with them.

Approach:

- Identify authoritative controller.
- validate custom resource intent rather than generated child objects where possible.
- avoid mutating operator-owned fields.
- use narrow exclusions.
- test operator upgrades.
- monitor reconciliation loops.
- coordinate with CRD schema.
- document approved exceptions.

---

# 14. Corporate L3 Troubleshooting Scenarios

## Q113. All resource creations are timing out after a Kyverno change. What do you do?

### Answer

1. Declare admission-control incident.
2. capture exact errors and affected API operations.
3. inspect API server webhook metrics/logs.
4. inspect Kyverno pods, endpoints, logs, CPU, and memory.
5. identify the changed policy or webhook configuration.
6. check external calls and registry latency.
7. disable or roll back the specific policy if possible.
8. use approved emergency bypass only if the cluster is blocked.
9. preserve evidence.
10. restore fail-closed behavior after remediation.
11. perform root-cause review.

Do not delete all webhook configurations as the first action unless the documented emergency procedure requires it and security leadership accepts the enforcement gap.

---

## Q114. Kyverno says it lacks permission when installing a generate policy. Why?

### Answer

Kyverno validates whether the background controller can perform required target operations.

Check:

```bash
kubectl auth can-i create <resource> \
  --as=system:serviceaccount:kyverno:<background-sa> \
  -n <namespace>
```

Inspect:

```bash
kubectl get clusterrole,role -A | grep kyverno
kubectl get clusterrolebinding,rolebinding -A | grep kyverno
kubectl logs -n kyverno deploy/kyverno-background-controller
```

Add minimal permissions through supported RBAC configuration. Do not bypass the validation without understanding the runtime failure.

---

## Q115. A policy is in Enforce but a resource was admitted. How do you investigate?

### Answer

Check:

- Did the rule match the correct kind and API version?
- Was the resource excluded?
- Did preconditions skip?
- Was the rule generated by autogen?
- Was the request a subresource?
- Was failure action overridden at rule level?
- Did webhook selectors omit the namespace?
- Did resourceFilters exclude it?
- Was the webhook unavailable with `failurePolicy: Ignore`?
- Did another mutating webhook change the object?
- Was the policy installed after the resource?
- Was it a background report, not admission?
- Does PolicyException match it?

Use CLI and in-cluster reproduction with logs at an appropriate verbosity.

---

## Q116. A policy works for Pods but not Deployments. Why?

### Answer

Possible causes:

- Autogen disabled or ineligible.
- rule path assumes Pod fields at root.
- Deployment template path not handled.
- generated rule excluded.
- exception refers only to original rule name.
- match kind only includes Pod.
- mutation feature is not autogen-compatible.
- custom workload controller unsupported.

Inspect policy status/generated rules and test an actual Deployment.

---

## Q117. A generate policy remains Pending. What do you check?

### Answer

```bash
kubectl get ur -A
kubectl describe ur <name> -n kyverno
kubectl logs -n kyverno deploy/kyverno-background-controller
kubectl get events -n kyverno --sort-by=.lastTimestamp
```

Check:

- Background controller health.
- leader election.
- RBAC.
- source clone resource.
- target namespace.
- target API/CRD.
- API throttling.
- invalid generated data.
- policy updates.
- finalizers.
- queue depth.

---

## Q118. PolicyReports show errors instead of failures. What does that mean?

### Answer

A failure normally means the resource was successfully evaluated and found non-compliant.

An error means policy evaluation could not complete correctly.

Possible errors:

- Missing variables.
- invalid JMESPath/CEL.
- external call failure.
- registry authentication failure.
- type mismatch.
- malformed policy.
- unavailable API.
- timeout.
- permission denial.

Treat engine errors as reliability/security defects because they may create enforcement gaps depending on failure configuration.

---

## Q119. A policy causes continuous GitOps drift. What do you do?

### Answer

1. Identify the exact fields changed by Kyverno.
2. determine field manager and authoritative source.
3. inspect server-side apply ownership.
4. compare rendered Git manifest to stored object.
5. decide whether:
   - Git should include the field.
   - Kyverno should validate only.
   - mutation should be removed.
   - a narrow diff ignore is acceptable.
6. test idempotency.
7. monitor for update loops.

Do not globally ignore `metadata`, `spec`, or securityContext drift.

---

## Q120. Image verification suddenly fails for all private images. What do you check?

### Answer

Check:

- Registry availability.
- DNS and proxy.
- certificate trust.
- registry credential Secret.
- ServiceAccount imagePullSecrets.
- credential expiry.
- changed secret namespace.
- policy image matching.
- signature repository location.
- key or keyless issuer.
- transparency service access.
- image digest.
- rate limiting.
- Kyverno version changes.
- clock skew.

Use a known-good signed image and verify independently with Cosign.

---

## Q121. AdmissionReports are accumulating. What do you do?

### Answer

- Check reports-controller pods and restarts.
- inspect logs.
- check leader election.
- inspect CPU/memory and throttling.
- check API client QPS/burst.
- inspect report count.
- verify cleanup CronJobs.
- check RBAC.
- check API server throttling.
- scale vertically where leader work is the bottleneck.
- tune rate limits only with monitoring.

---

## Q122. A PolicyException does not work. What do you check?

### Answer

- Are PolicyExceptions enabled?
- Correct API group and version?
- Correct namespace?
- Does exception select the policy and generated/autogen rule name?
- Does CEL condition match?
- Is background behavior expected?
- Does exception match resource kind/name/namespace?
- Is policy type supported?
- Was exception denied by another policy?
- Is there a typo in rule name?
- Is exception in the namespace Kyverno searches?

---

## Q123. A new policy blocks the Kyverno controller itself. How do you recover?

### Answer

1. Use cluster-admin or approved break-glass access.
2. identify the policy and matching rule.
3. patch policy to Audit or add a narrowly scoped exclusion.
4. verify Kyverno pods recover.
5. remove the temporary bypass after testing.
6. add a policy test for Kyverno's own resources.
7. review system namespace rollout controls.

Avoid permanently excluding all `kube-system` or `kyverno` resources from every policy without threat analysis.

---

## Q124. Background scans overload the API server. What changes would you consider?

### Answer

- Narrow policy match.
- disable background for admission-only policies.
- reduce expensive API calls.
- tune scan intervals where supported.
- scale reports/background resources appropriately.
- reduce report volume.
- stagger policy rollout.
- use GlobalContextEntry or cached data where appropriate.
- avoid list-all queries.
- tune client rate limits carefully.
- monitor API priority and fairness.
- partition compliance evaluation by policy scope.

---

## Q125. A policy update immediately creates thousands of writes. Why?

### Answer

Likely causes:

- mutate-existing enabled broadly.
- generateExisting behavior.
- synchronization.
- broad trigger match.
- policy update retriggered reconciliation.
- cleanup/generate target expansion.
- changed context data.
- multiple controllers reacting to the same updates.

Stop or narrow the policy, preserve evidence, inspect UpdateRequests, and assess API server/etcd impact.

---

## Q126. How do you perform root-cause analysis for a Kyverno production incident?

### Answer

Collect:

- Timeline.
- policy Git commit.
- Kyverno and chart versions.
- Helm values.
- webhook configurations.
- controller logs.
- API server audit and webhook metrics.
- affected requests.
- PolicyReports.
- UpdateRequests.
- events.
- resource usage.
- network and DNS evidence.
- external service/registry status.
- exact recovery actions.

Document:

- Trigger.
- blast radius.
- detection gap.
- why controls failed.
- corrective action.
- preventive action.
- owner and deadline.
- test added.
- rollback improvement.

---

# 15. Rapid-Fire Commands and Diagnostic Checklist

## Installation and Health

```bash
helm list -n kyverno
helm get values kyverno -n kyverno
helm get manifest kyverno -n kyverno

kubectl get pods -n kyverno -o wide
kubectl get deploy -n kyverno
kubectl get svc,endpoints,endpointslices -n kyverno
kubectl get events -n kyverno --sort-by=.lastTimestamp
```

## Policies

```bash
kubectl get policy -A
kubectl get clusterpolicy
kubectl get validatingpolicy
kubectl get mutatingpolicy
kubectl get imagevalidatingpolicy
kubectl get cleanupolicy -A
kubectl get clustercleanupolicy
kubectl get policyexception -A
```

> Resource short names and exact plurals can vary by API/version. Use `kubectl api-resources | grep -i kyverno`.

## Reports

```bash
kubectl get policyreports -A
kubectl get clusterpolicyreports
kubectl get admissionreports -A
kubectl get backgroundscanreports -A
```

## Background Processing

```bash
kubectl get updaterequests -A
kubectl get ur -A
kubectl describe ur <name> -n kyverno
```

## Webhooks

```bash
kubectl get validatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations
kubectl get validatingwebhookconfiguration <name> -o yaml
kubectl get mutatingwebhookconfiguration <name> -o yaml
```

## RBAC

```bash
kubectl auth can-i --list \
  --as=system:serviceaccount:kyverno:<service-account>

kubectl auth can-i create networkpolicies \
  --as=system:serviceaccount:kyverno:<background-service-account> \
  -n application-namespace
```

## Logs

```bash
kubectl logs -n kyverno deploy/kyverno-admission-controller --since=30m
kubectl logs -n kyverno deploy/kyverno-background-controller --since=30m
kubectl logs -n kyverno deploy/kyverno-reports-controller --since=30m
kubectl logs -n kyverno deploy/kyverno-cleanup-controller --since=30m
```

## CLI

```bash
kyverno version
kyverno apply policy.yaml --resource resource.yaml
kyverno apply ./policies --resource ./resources --policy-report
kyverno test ./tests
kyverno test ./tests --detailed-results
kyverno test ./tests -o junit
kyverno jp query 'spec.containers[].image' resource.yaml
```

## Cluster API Discovery

```bash
kubectl api-resources | grep -i kyverno
kubectl explain clusterpolicy
kubectl explain clusterpolicy.spec
kubectl get crd | grep -i kyverno
```

---

## Senior Interview Response Framework

For every production scenario, answer in this order:

1. **Scope:** Which clusters, namespaces, policies, and request operations are affected?
2. **Safety:** Is admission blocked or is policy failing open?
3. **Evidence:** Capture policy version, object IDs, logs, reports, and webhook state.
4. **Containment:** Roll back or disable only the faulty policy where possible.
5. **Recovery:** Restore controller/webhook health and validate real admission.
6. **Compliance:** Identify resources admitted during any enforcement gap.
7. **Prevention:** Add tests, monitoring, rollout gates, and governance.
8. **Communication:** Record risk, owners, timelines, and exception status.

---

# 16. Official References

- Kyverno documentation: https://kyverno.io/docs/
- Introduction: https://kyverno.io/docs/introduction/
- How Kyverno works: https://kyverno.io/docs/introduction/how-kyverno-works/
- Installation: https://kyverno.io/docs/installation/installation/
- High availability: https://kyverno.io/docs/guides/high-availability/
- Scaling: https://kyverno.io/docs/installation/scaling/
- Classic validate rules: https://kyverno.io/docs/policy-types/cluster-policy/validate/
- Mutate rules: https://kyverno.io/docs/policy-types/cluster-policy/mutate/
- Generate rules: https://kyverno.io/docs/policy-types/cluster-policy/generate/
- Variables: https://kyverno.io/docs/policy-types/cluster-policy/variables/
- Preconditions: https://kyverno.io/docs/policy-types/cluster-policy/preconditions/
- External data: https://kyverno.io/docs/policy-types/cluster-policy/external-data-sources/
- ValidatingPolicy: https://kyverno.io/docs/policy-types/validating-policy/
- MutatingPolicy: https://kyverno.io/docs/policy-types/mutating-policy/
- ImageValidatingPolicy: https://kyverno.io/docs/policy-types/image-validating-policy/
- CleanupPolicy: https://kyverno.io/docs/policy-types/cleanup-policy/
- Policy exceptions: https://kyverno.io/docs/guides/exceptions/
- Policy reports: https://kyverno.io/docs/guides/reports/
- Monitoring: https://kyverno.io/docs/guides/monitoring/
- Metrics reference: https://kyverno.io/docs/reference/metrics/
- Troubleshooting: https://kyverno.io/docs/guides/troubleshooting/
- Security: https://kyverno.io/docs/guides/security/
- Kyverno CLI: https://kyverno.io/docs/subprojects/kyverno-cli/
- Testing policies: https://kyverno.io/docs/guides/testing-policies/
- Applying policies: https://kyverno.io/docs/guides/applying-policies/
- Kyverno GitHub releases: https://github.com/kyverno/kyverno/releases
- Kyverno policy library: https://github.com/kyverno/policies

---

## End of Document
