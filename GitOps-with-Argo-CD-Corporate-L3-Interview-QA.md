
# GitOps with Argo CD — Corporate L3 Interview Questions and Detailed Answers

> **Audience:** Senior Kubernetes/OpenShift administrators, DevOps engineers, platform engineers, SREs, technical leads, and candidates with approximately 8–12 years of infrastructure experience.
>
> **Version note (validated 2026-08-01):** The production examples use the Argo CD 3.4 stable line as the reference baseline. Always compare the installed version with its exact upgrade notes before applying commands. ApplicationSet Progressive Syncs are treated as a beta capability and must be enabled, tested, and governed accordingly.
>
> **Interview level:** Corporate L3 / production operations / design and troubleshooting.

---

## How to Use This Guide

A strong L3 answer should not stop at a definition. Structure your response in five layers:

1. State the design principle or direct answer.
2. Explain how Argo CD implements it internally.
3. Describe the production risk and security boundary.
4. Give a concrete manifest, command, metric, or log location.
5. Finish with validation, rollback, and operational ownership.

The examples assume Kubernetes-compatible clusters and the namespace `argocd`. For OpenShift GitOps, translate installation and exposure details to the Operator-managed `ArgoCD` custom resource and OpenShift Route model.

---

## Contents

1. GitOps Fundamentals and Enterprise Design
2. Argo CD Architecture and Installation
3. Applications, Projects, Repositories, and Repository Design
4. Reconciliation, Sync, Drift, Health, and Deployment Ordering
5. Helm, Kustomize, Jsonnet, Multiple Sources, and Plugins
6. ApplicationSet, Multi-Cluster, and Fleet Management
7. Security, RBAC, SSO, Secrets, and Governance
8. High Availability, Scale, Performance, and Observability
9. Operations, Backup, Disaster Recovery, Upgrade, and OpenShift GitOps
10. Corporate L3 Scenarios and Troubleshooting
11. Enterprise Reference Manifests
12. Command and Troubleshooting Cheat Sheet
13. Mock Interview Labs
14. Official References

---

# 1. GitOps Fundamentals and Enterprise Design

## Q1. What is GitOps, and how is it different from ordinary CI/CD?
**Answer:** GitOps is an operating model in which the desired state of an environment is declaratively stored in version control and an automated reconciler continuously compares that desired state with the live system. CI builds and verifies an artifact; GitOps CD promotes a reviewed configuration change and reconciles the cluster to it.
### Detailed explanation
- Git becomes the auditable source of desired state, not merely a place where scripts are stored.
- The deployment mechanism is pull-based from the cluster side. Argo CD reads Git and applies changes using its cluster identity; the CI system does not require broad production credentials.
- Reconciliation is continuous. A manual cluster change is detected as drift instead of silently becoming the new truth.
- Rollback is normally a Git revert or a new corrective commit. The complete change history, approval trail, and deployment intent remain reviewable.
### Example / commands

```text
Application source code -> CI tests/builds image -> registry
                                      |
                                      v
                           CI updates GitOps repository
                                      |
                                      v
                         Argo CD detects Git revision
                                      |
                                      v
                          Kubernetes/OpenShift cluster
```
### L3 risks and common mistakes
- Calling any deployment triggered from Git 'GitOps' even when a pipeline uses `kubectl` with cluster-admin credentials.
- Allowing emergency manual changes without a defined process to back-port the final state to Git.
- Treating Git as a secret store. GitOps requires external secret management or encrypted secret workflows.

---
## Q2. What are the core GitOps principles you would enforce in an enterprise?
**Answer:** The core principles are declarative desired state, versioned and immutable change history, automated reconciliation, explicit authorization, and observable convergence.
### Detailed explanation
- All deployable state should be reproducible from Git plus explicitly managed external dependencies such as secrets and artifact registries.
- Changes should use pull requests, protected branches, mandatory review, status checks, and signed commits or tags where required.
- The reconciler should operate continuously with defined sync policy, retry behavior, health checks, and alerting.
- Production promotion must be a change to configuration, not an undocumented click or direct cluster mutation.
- Ownership must be clear: application teams own application configuration; the platform team owns Argo CD, cluster registration, shared policy, and guardrails.
### L3 risks and common mistakes
- Using a single unprotected branch for development and production.
- Granting teams permission to change both platform policy and their workload in the same review path.
- Ignoring dependency availability, such as a deleted Helm chart or unreachable Git server, which makes the declared state non-reproducible.

---
## Q3. Why is the pull model safer than a push-based deployment pipeline?
**Answer:** The pull model reduces the number of systems that need production credentials and moves authorization to a continuously running, policy-controlled controller inside the management boundary.
### Detailed explanation
- A CI runner only needs permission to publish an image and propose a Git change. It does not need direct access to every production API server.
- Argo CD uses dedicated service accounts and project restrictions, which can be reviewed centrally.
- Network controls can permit outbound access from Argo CD to Git and target clusters without permitting inbound access from arbitrary CI workers.
- Compromise of a CI runner still remains serious, but the attacker must pass repository controls and Argo CD policy instead of directly issuing cluster commands.
### L3 risks and common mistakes
- A pull model is not automatically secure. If branch protection is weak or Argo CD has cluster-admin across all clusters, the blast radius remains large.
- Do not expose long-lived Argo CD API tokens to CI merely to recreate a push deployment pattern. Prefer Git commit plus webhook or normal reconciliation.

---
## Q4. What does reconciliation mean in Argo CD?
**Answer:** Reconciliation is the control loop that obtains desired manifests from the configured source, compares them with live resources, calculates status, and—when policy permits—performs operations that make the live state converge to Git.
### Detailed explanation
- The repo-server renders manifests from Git, Helm, Kustomize, Jsonnet, OCI, or a Config Management Plugin.
- The application-controller queries the target cluster, tracks managed resources, computes differences, assesses health, and runs sync operations.
- Reconciliation is not only deployment. It also detects drift, deleted resources, orphaned resources, inaccessible repositories, invalid manifests, and health degradation.
- A webhook can reduce detection latency, but periodic reconciliation remains essential because webhooks can be missed.
### Example / commands

```bash
argocd app get payments-prod --refresh
argocd app diff payments-prod
argocd app history payments-prod
kubectl -n argocd logs statefulset/argocd-application-controller --since=15m
```
### L3 risks and common mistakes
- Confusing `Synced` with `Healthy`. A resource can match Git but still be unhealthy.
- Confusing `OutOfSync` with an application failure. It can simply indicate intentional drift or a pending approved change.

---
## Q5. What is configuration drift, and how should it be handled?
**Answer:** Configuration drift is any difference between the desired object rendered from Git and the live object observed in the cluster. The correct response depends on whether the difference is unauthorized, controller-generated, operationally necessary, or a false-positive caused by normalization.
### Detailed explanation
- First classify the field owner: Git, Kubernetes defaulting, mutating webhook, autoscaler, operator, or human.
- For unauthorized human changes, restore through self-heal or a reviewed sync and investigate the access path.
- For legitimate controller-owned fields, configure narrowly scoped `ignoreDifferences` rules instead of ignoring the whole resource.
- For an emergency live fix, capture evidence, make the minimal change, then immediately create a Git change that represents the accepted final state.
### Example / commands

```yaml
spec:
  ignoreDifferences:
    - group: apps
      kind: Deployment
      name: payments
      namespace: payments-prod
      jsonPointers:
        - /spec/replicas
```

Use this only when another approved controller, such as an HPA, owns the ignored field.
### L3 risks and common mistakes
- Broad `jqPathExpressions` or managed-fields exclusions can hide real security changes.
- Enabling self-heal before understanding operator or webhook mutations can cause an endless reconciliation fight.

---
## Q6. What is the difference between desired state, live state, sync status, and health status?
**Answer:** Desired state is the rendered output from the configured Git/Helm/OCI sources. Live state is what the cluster API currently stores. Sync status compares desired and live state; health status evaluates whether the live resources are operational.
### Detailed explanation
- `Synced` means Argo CD sees no relevant diff after normalization and configured exclusions.
- `OutOfSync` means at least one tracked resource differs, is missing, or requires pruning.
- `Healthy` means resource-specific health logic considers the application operational.
- `Progressing`, `Degraded`, `Suspended`, `Missing`, and `Unknown` describe different runtime conditions and may require custom health logic for CRDs.
### Example / commands

```bash
argocd app get checkout-prod -o json | jq '.status | {
  sync: .sync.status,
  health: .health.status,
  revision: .sync.revision,
  operation: .operationState.phase
}'
```
### L3 risks and common mistakes
- A `Synced/Degraded` application often indicates a valid Git configuration that produces an unhealthy workload.
- A `OutOfSync/Healthy` application can indicate a harmless replica drift, but it still needs ownership analysis.

---
## Q7. How would you design separation of duties for GitOps?
**Answer:** Use separate identities, repositories, review paths, Argo CD projects, and cluster permissions so that no single routine actor can unilaterally change code, deployment policy, secrets, and production runtime.
### Detailed explanation
- Developers modify source code and approved application-level configuration.
- Release engineering or service owners approve environment promotion.
- Platform engineering owns Argo CD installation, cluster registration, global RBAC, trusted repositories, and policy controls.
- Security teams define admission policy and audit requirements without becoming a deployment bottleneck.
- Break-glass access is time-bound, separately authenticated, fully audited, and followed by reconciliation into Git.
### Example / commands

```text
Source repo PR        -> application reviewers
Environment repo PR   -> service owner + operations
Platform repo PR      -> platform SRE + security
Production sync       -> automated in allowed window or privileged operator
```
### L3 risks and common mistakes
- Putting `argocd-rbac-cm`, production Applications, and developer manifests in one repository controlled by the same team.
- Using a shared local admin account instead of SSO groups and individual audit identities.

---
## Q8. Monorepo or polyrepo for GitOps—what would you choose?
**Answer:** There is no universal choice. Select the repository model based on ownership boundaries, scale, release coupling, access control, and repo-server rendering cost.
### Detailed explanation
- A monorepo provides atomic cross-service changes and consistent policy but can create broad access, large clones, noisy webhooks, and expensive manifest generation.
- A polyrepo model aligns strongly with team ownership and minimizes blast radius but increases repository and dependency-management overhead.
- A common enterprise compromise is separate application source repositories plus one or more environment/configuration repositories grouped by business domain or platform boundary.
- Use `argocd.argoproj.io/manifest-generate-paths` carefully in monorepos to reduce unnecessary refresh work when supported by the source pattern.
### L3 risks and common mistakes
- Creating one repository per tiny manifest and overwhelming operations with credentials, webhooks, and policy duplication.
- Creating a single global repository where every team can modify every cluster.

---
## Q9. What is an environment promotion strategy in GitOps?
**Answer:** Promotion is a controlled change that advances a tested artifact and its configuration from one environment boundary to another. The artifact should normally remain immutable; only the environment reference changes.
### Detailed explanation
- Build the image once and promote the digest, not a mutable tag such as `latest`.
- Use pull requests to update the target environment overlay, Helm values, or application parameter in Git.
- Require environment-specific checks such as policy validation, security scans, integration tests, and approval.
- Use separate branches only when the organization can manage merge drift; directory or repository-per-environment patterns are often easier to audit.
- Record the image digest, Git revision, application revision, approver, and deployment result.
### Example / commands

```yaml
# environments/prod/payments/kustomization.yaml
images:
  - name: registry.example.com/payments
    newName: registry.example.com/payments
    newTag: "sha256:4f8c...immutable-digest"
```
### L3 risks and common mistakes
- Rebuilding an image for each environment destroys artifact provenance.
- Promoting `HEAD` or `main` directly to production makes rollback and audit ambiguous.

---
## Q10. What are the most important anti-patterns in enterprise GitOps?
**Answer:** The most damaging anti-patterns are unrestricted production scope, mutable artifact references, secrets in Git, imperative pipeline deployments, weak review controls, and unobservable reconciliation.
### Detailed explanation
- One Argo CD instance with cluster-admin on every cluster and no AppProject restrictions.
- Applications pointing to mutable branches or chart versions without promotion controls.
- Teams using `argocd app set` parameter overrides that are not represented in Git.
- Ignoring entire resource kinds to eliminate diff noise.
- Using the App-of-Apps pattern without controlling child-application privileges.
- No alert for reconciliation failure, repository failure, expiring credentials, or degraded applications.
- No tested backup and restore process for Argo CD configuration.

---

# 2. Argo CD Architecture and Installation

## Q11. Explain the major Argo CD components and their responsibilities.
**Answer:** The core components are the API server, repository server, application controller, Redis, Dex or external OIDC integration, and optional ApplicationSet and Notifications controllers.
### Detailed explanation
- `argocd-server` serves the UI, CLI, REST/gRPC API, authentication integration, RBAC enforcement, and webhook endpoint.
- `argocd-repo-server` clones repositories and renders desired manifests. It is a high-risk execution boundary because Helm, Kustomize, and plugins process repository-controlled content.
- `argocd-application-controller` watches Applications and clusters, calculates state, evaluates health, and executes sync operations.
- Redis is used for caching and coordination; the HA installation uses a highly available Redis topology.
- The ApplicationSet controller generates and maintains Applications. Notifications evaluates triggers and sends messages to configured services.
### Example / commands

```bash
kubectl -n argocd get deploy,statefulset,pod
kubectl -n argocd get cm,secret | grep argocd
kubectl -n argocd get svc,endpoints
```
### L3 risks and common mistakes
- Troubleshooting only the UI pod. Manifest failures frequently originate in repo-server, while state and sync failures commonly originate in application-controller.
- Running untrusted config-management plugins in the same repo-server security context without isolation.

---
## Q12. Describe the end-to-end flow when a Git commit is deployed.
**Answer:** A repository change triggers or is discovered by refresh; repo-server fetches the revision and renders manifests; application-controller compares them with live state; a manual or automated sync creates an operation; resources are applied in phase/wave order and health is monitored.
### Detailed explanation
- The API server receives webhook events but does not itself apply workloads.
- The repository server returns generated manifests and related source metadata.
- The application controller communicates with target-cluster APIs using registered cluster credentials.
- Operation status, resource results, health, and history are written to the Application status.
- Notifications can react to sync success, failure, health degradation, or custom expressions.
### Validation
Correlate the Git SHA across:

```bash
git rev-parse HEAD
argocd app get APP -o json | jq -r '.status.sync.revision'
argocd app history APP
kubectl -n TARGET get deploy APP -o jsonpath='{.metadata.annotations}'
```

---
## Q13. How do you install Argo CD safely in production?
**Answer:** Use a pinned, supported release; choose HA manifests or a supported Operator; apply server-side; expose the API securely; integrate SSO; remove or tightly control the local admin; configure Projects and RBAC before onboarding teams; and validate backup, monitoring, and network policy.
### Detailed explanation
- Create a dedicated namespace and use the published HA installation for a multi-node production control plane.
- Pin the exact version instead of applying a moving `stable` URL in controlled environments.
- Use trusted TLS certificates and a supported ingress, gateway, or OpenShift Route configuration.
- Define resource requests/limits, PodDisruptionBudgets, topology spread, anti-affinity, and persistent storage where operationally justified.
- Verify image signatures and provenance according to organizational supply-chain policy.
### Example / commands

```bash
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/v3.4.6/manifests/ha/install.yaml

kubectl -n argocd rollout status deployment/argocd-server
kubectl -n argocd rollout status statefulset/argocd-application-controller
```
### L3 risks and common mistakes
- Blindly installing the newest release candidate in production.
- Exposing the API server without TLS or relying on a public default admin password.
- On OpenShift, directly editing Operator-managed Deployments instead of changing the `ArgoCD` CR.

---
## Q14. When should you use the non-HA and HA installations?
**Answer:** Use non-HA for development, labs, or low-criticality environments where a temporary controller outage is acceptable. Use HA for production management planes that require resilience, scaling, and controlled maintenance.
### Detailed explanation
- HA increases replicas for stateless components and changes application-controller and Redis topology.
- HA does not eliminate every failure mode. Git, DNS, target-cluster APIs, storage, and identity providers remain dependencies.
- A small environment may still choose HA because Argo CD controls many critical clusters; size is not the only criterion.
- Document the recovery objective. Argo CD outage normally stops reconciliation but does not stop already running workloads.
### L3 risks and common mistakes
- Assuming HA means disaster recovery. A namespace deletion, credential corruption, or regional failure still requires backup and restoration.
- Scaling every component equally instead of tuning the bottleneck component.

---
## Q15. How does Argo CD store repository and cluster credentials?
**Answer:** Argo CD stores repository and cluster connection definitions as labeled Kubernetes Secrets in its namespace. The API server manages them; repo-server consumes repository credentials; application-controller consumes target-cluster credentials.
### Detailed explanation
- Repository secrets contain URLs and authentication material such as SSH keys, tokens, or username/password pairs.
- Repository credential templates can match URL prefixes and reduce duplicated secrets.
- Cluster secrets contain the API server address, cluster name, and connection configuration, normally including bearer-token or exec-provider information.
- Kubernetes encryption at rest, namespace RBAC, external secret integration, credential rotation, and audit logging are required because these secrets define Argo CD's reach.
### Example / commands

```bash
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=repo-creds
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=cluster
```
### L3 risks and common mistakes
- Backing up secrets to an unencrypted location.
- Using one broad token for all clusters rather than scoped service accounts and rotation.

---
## Q16. Why does Argo CD use Redis, and what happens if Redis fails?
**Answer:** Redis caches application, repository, and cluster-related data and supports coordination. A Redis outage can degrade UI/API responsiveness and reconciliation, but Git and the cluster remain the authoritative sources rather than Redis itself.
### Detailed explanation
- In HA mode, protect Redis availability and authentication according to the installation model.
- Investigate Redis latency, memory pressure, evictions, failed connections, and pod restarts when applications appear stale across multiple components.
- Do not treat Redis persistence as the only backup. The important durable configuration is represented by Kubernetes objects and external systems.
### Example / commands

```bash
kubectl -n argocd get pod -l app.kubernetes.io/name=argocd-redis
kubectl -n argocd logs deploy/argocd-server --since=15m | grep -i redis
kubectl -n argocd logs statefulset/argocd-application-controller --since=15m | grep -i redis
```

---
## Q17. How does Argo CD communicate with remote clusters?
**Answer:** The application controller uses the API endpoint and credentials stored in the registered cluster secret. It builds Kubernetes clients, watches or lists resources, performs discovery, and applies changes according to the Application's destination and Project policy.
### Detailed explanation
- Network routing, DNS, TLS trust, API server authorization, service-account token validity, and Kubernetes API limits all affect connectivity.
- For large fleets, avoid a single unrestricted credential. Scope namespaces and cluster resources where feasible.
- Test connection from the application-controller network context, not only from an administrator laptop.
- A cluster can be registered declaratively or with `argocd cluster add`; declarative registration is preferable for repeatability.
### Example / commands

```bash
argocd cluster list
argocd cluster get https://kubernetes.default.svc
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=cluster -o yaml
```
### L3 risks and common mistakes
- The convenient `argocd cluster add` workflow can create broad permissions. Review the generated service account, ClusterRole, and binding.
- Disabling TLS verification to work around certificate errors creates a man-in-the-middle risk.

---
## Q18. What ports and network flows must be considered?
**Answer:** Model network flows per component rather than opening the namespace broadly. The server needs client/ingress and identity-provider connectivity; repo-server needs Git, Helm, OCI, and plugin dependencies; application-controller needs target API servers; notifications needs outbound access to delivery services.
### Detailed explanation
- Account for DNS, time synchronization, certificate revocation or OCSP where used, proxies, and private CAs.
- Use default-deny NetworkPolicies and add narrowly scoped egress/ingress rules.
- Webhook delivery must reach the Argo CD API endpoint, but it does not need to bypass authentication or expose other components.
- Remote clusters may require firewall allowlists from Argo CD worker-node or egress gateway addresses.
### L3 risks and common mistakes
- Allowing repo-server unrestricted internet access even though it executes repository-controlled tooling.
- Forgetting outbound access to OIDC discovery, JWKS, Git LFS, OCI registries, or chart dependencies.

---
## Q19. How do you expose the Argo CD API/UI behind an ingress or OpenShift Route?
**Answer:** Terminate TLS at a clearly defined layer and configure the server and proxy consistently for HTTP/2 gRPC and browser traffic. On OpenShift, use the Operator-supported Route settings rather than manually modifying managed resources.
### Detailed explanation
- Decide whether TLS terminates at Argo CD, the ingress, or both. Avoid accidental plaintext outside the trusted boundary.
- Some ingress controllers require separate handling for gRPC and HTTPS or the CLI may need `--grpc-web`.
- Set the external URL used by SSO callbacks and notifications.
- Validate forwarded headers, session cookies, maximum header size, idle timeout, and WebSocket/stream behavior.
### Example / commands

```bash
argocd login argocd.example.com --sso --grpc-web
argocd version
curl -Ik https://argocd.example.com
```
### L3 risks and common mistakes
- Using `server.insecure: true` without understanding where TLS is terminated.
- SSO redirect loops often result from an incorrect external URL, callback URL, proxy header, or cookie policy.

---
## Q20. How do you bootstrap Argo CD declaratively without creating a circular dependency?
**Answer:** Use a controlled two-stage bootstrap. Install the minimum Argo CD control plane and root trust manually or through infrastructure automation, then let a tightly governed root Application/ApplicationSet manage the remaining Argo CD configuration and workloads.
### Detailed explanation
- The bootstrap stage should establish namespace, CRDs, controller workloads, trusted repository credentials, root Project, and root Application.
- Protect the root repository and application because they can create child Applications and potentially expand privileges.
- Define a recovery path that can reinstall Argo CD and reapply the root objects even when Argo CD is unavailable.
- Avoid allowing the root application to prune the namespace or its own critical credentials without explicit safeguards.
### Example / commands

```text
Stage 1: Terraform/Ansible/Operator installs Argo CD + root Secret/Application
Stage 2: Root Application reconciles AppProjects, RBAC, Applications, policies
Stage 3: Team Applications reconcile workloads
```

---

# 3. Applications, Projects, Repositories, and Repository Design

## Q21. What is an Argo CD Application resource?
**Answer:** An Application is the declarative relationship between one or more configuration sources and a destination cluster/namespace, together with project membership, sync policy, diff options, and status.
### Detailed explanation
- `spec.source` or `spec.sources` defines where and how desired manifests are generated.
- `spec.destination` identifies a registered cluster by server URL or name and an optional namespace.
- `spec.project` applies the security and governance boundary.
- `spec.syncPolicy` controls automation, pruning, self-healing, retry, and sync options.
- The status contains current revision, sync state, health, conditions, resource summaries, and operation history.
### Example / commands

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: payments-prod
  namespace: argocd
spec:
  project: payments
  source:
    repoURL: ssh://git@git.example.com/platform/env-config.git
    targetRevision: refs/tags/payments-prod-2026.08.01
    path: environments/prod/payments
  destination:
    name: prod-cluster-01
    namespace: payments-prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

---
## Q22. What is an AppProject, and why is it a critical security boundary?
**Answer:** An AppProject groups Applications and restricts allowed source repositories, destination clusters/namespaces, cluster-scoped and namespace-scoped resource kinds, roles, sync windows, and orphaned-resource behavior.
### Detailed explanation
- A Project prevents an application from pointing at an arbitrary repository or deploying into an unauthorized namespace.
- Cluster-resource allow/deny lists reduce the risk that application teams create high-impact objects such as ClusterRoles, CRDs, or webhooks.
- Project roles can issue JWTs or map SSO groups to project-specific permissions, but global RBAC remains the central policy layer.
- Treat changes to the default project as privileged; unrestricted defaults can defeat otherwise good RBAC.
### Example / commands

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: payments
  namespace: argocd
spec:
  sourceRepos:
    - ssh://git@git.example.com/payments/*
  destinations:
    - name: prod-cluster-01
      namespace: payments-*
  clusterResourceWhitelist:
    - group: ""
      kind: Namespace
  namespaceResourceBlacklist:
    - group: rbac.authorization.k8s.io
      kind: RoleBinding
```
### L3 risks and common mistakes
- Leaving the `default` project unrestricted and letting teams create Applications in it.
- Allowing arbitrary Helm repositories, because a chart is executable deployment input.

---
## Q23. What is the difference between destination `server` and destination `name`?
**Answer:** `server` identifies the exact Kubernetes API URL; `name` refers to the name stored in the registered cluster secret. Use one method consistently and ensure ApplicationSet templates resolve the intended cluster.
### Detailed explanation
- The in-cluster API is commonly `https://kubernetes.default.svc`.
- Cluster names are easier for human-readable templates but must be unique and maintained as fleet metadata.
- AppProject destinations can also use name or server matching, so an inconsistent choice can produce permission errors.
- During cluster replacement, a stable logical name can simplify migration, but credentials and labels must be updated carefully.

---
## Q24. How should GitOps repositories be structured for multiple environments?
**Answer:** Use a structure that makes ownership and promotion explicit. A common design separates reusable bases or charts from environment-specific overlays and stores production configuration behind stricter review controls.
### Detailed explanation
- Keep application source and deployment configuration in separate repositories when this improves security and release independence.
- Prefer immutable artifact digests and explicit chart versions.
- Place common policy and base configuration in reusable components, but avoid inheritance so deep that reviewers cannot determine the final manifest.
- Validate rendered output in pull requests with `helm template`, `kustomize build`, schema checks, policy checks, and server-side dry runs against a representative API version set.
### Example / commands

```text
env-config/
├── apps/
│   ├── payments/base/
│   └── checkout/base/
├── environments/
│   ├── dev/payments/
│   ├── stage/payments/
│   └── prod/payments/
├── clusters/
│   ├── prod-cluster-01/
│   └── prod-cluster-02/
└── platform/
    ├── projects/
    └── applicationsets/
```

---
## Q25. How do branch, tag, and commit SHA tracking strategies differ?
**Answer:** A branch provides continuous movement, a tag provides a named release boundary, and a commit SHA provides the strongest immutability and reproducibility.
### Detailed explanation
- Use branches for rapidly changing development environments when automatic deployment is desired.
- Use protected, preferably signed tags for controlled releases if tag movement is prohibited.
- Use commit SHAs or immutable OCI/chart versions for high-assurance production promotion.
- `HEAD` is convenient but ambiguous in audits; an application history should still resolve the exact deployed revision.
### L3 risks and common mistakes
- Mutable tags and force-pushed branches can make the same declared revision point to different content over time.
- A Git SHA does not make external Helm dependencies immutable unless their versions and registries are also pinned.

---
## Q26. How do you configure a private Git repository securely?
**Answer:** Use a repository Secret or credential template with the minimum required read access, validate TLS or SSH host keys, rotate credentials, and restrict which Projects may use the repository.
### Detailed explanation
- Prefer deploy keys or workload identity scoped to a specific repository over a developer's personal credential.
- For HTTPS, add the private CA to `argocd-tls-certs-cm` or the supported Operator field.
- For SSH, maintain trusted host keys in `argocd-ssh-known-hosts-cm` and plan host-key rotation.
- Never use `--insecure-skip-server-verification` in production as a permanent workaround.
### Example / commands

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: payments-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: ssh://git@git.example.com/payments/env-config.git
  sshPrivateKey: |
    -----BEGIN OPENSSH PRIVATE KEY-----
    <injected by external secret controller>
    -----END OPENSSH PRIVATE KEY-----
```

---
## Q27. What are repository credential templates?
**Answer:** Repository credential templates are labeled `repo-creds` Secrets that provide authentication for repository URLs matching a prefix. They reduce duplicate credentials and simplify rotation.
### Detailed explanation
- A template is used when the repository definition does not contain its own credential fields.
- Choose URL prefixes that are specific enough to prevent an unintended repository from inheriting powerful credentials.
- Separate credentials by trust domain, business unit, and read/write requirement.
- Audit both explicit repository Secrets and templates when diagnosing access behavior.
### Example / commands

```bash
kubectl -n argocd get secret \
  -l argocd.argoproj.io/secret-type=repo-creds -o yaml
argocd repocreds list
argocd repo list
```

---
## Q28. What is the App-of-Apps pattern, and what are its risks?
**Answer:** App-of-Apps uses a parent Application whose rendered resources include child Application objects. It provides hierarchical bootstrap and grouping, but the parent effectively delegates application-creation power to its source repository.
### Detailed explanation
- Use it for controlled platform bootstrap or domain grouping when repository ownership is strong.
- The parent Project must allow the Application CRD and destinations used by children.
- Sync ordering, deletion propagation, finalizers, and child ownership require explicit design.
- ApplicationSet is often more scalable and expressive for generated fleets, but it also creates Applications and therefore has a similar privilege concern.
### L3 risks and common mistakes
- Allowing an ordinary application team to modify a parent that can create Applications in privileged Projects.
- Using recursive parent/child structures that are hard to reason about and recover.

---
## Q29. How do Application finalizers affect deletion?
**Answer:** The Argo CD resources finalizer enables cascading deletion of managed resources when an Application is deleted. Without the finalizer, deleting the Application can orphan its workloads.
### Detailed explanation
- Foreground and background propagation behavior should match the operational intent.
- Before deleting, verify the destination cluster is reachable; otherwise finalizer processing can be blocked.
- For disaster recovery or migration, orphaning may be intentional, but it must be a deliberate and documented action.
- ApplicationSet deletion settings and child Application finalizers must be evaluated together.
### Example / commands

```yaml
metadata:
  finalizers:
    - resources-finalizer.argocd.argoproj.io
```

```bash
argocd app delete payments-prod --cascade
argocd app delete payments-prod --cascade=false
```
### L3 risks and common mistakes
- Removing a stuck finalizer without understanding which workloads will be orphaned.
- Accidentally pruning production because a generator no longer returns a cluster.

---
## Q30. How do you manage Applications declaratively versus with the CLI/UI?
**Answer:** Use declarative manifests for repeatability and policy. Use CLI/UI primarily for inspection, controlled operations, and emergency actions—not as the authoritative place to change source, destination, or production parameters.
### Detailed explanation
- Declarative Applications can be reviewed, promoted, restored, and diffed like other configuration.
- CLI commands are useful for sync, wait, diff, history, terminate operation, and diagnostic refresh.
- Parameter overrides set through `argocd app set` create state outside the Git workflow and should be avoided or immediately captured in Git.
- Restrict `applications, update` and `applications, override` permissions to prevent bypassing repository review.

---

# 4. Reconciliation, Sync, Drift, Health, and Deployment Ordering

## Q31. What happens during an Argo CD sync?
**Answer:** Argo CD selects the target revision, generates manifests, calculates required create/update/delete actions, orders resources by phase, wave, kind, and name, applies them, waits according to health and hook semantics, and records the operation result.
### Detailed explanation
- A sync can be full or selective, manual or automated, and can include pruning.
- Apply strategy is influenced by sync options such as server-side apply, replace, force, prune-last, validation, and namespace creation.
- Hooks can run before, during, after, or on failure of the main sync.
- The operation can fail even when some resources were successfully applied; inspect per-resource results.
### Example / commands

```bash
argocd app sync payments-prod --revision <git-sha>
argocd app wait payments-prod --operation --health --timeout 600
argocd app get payments-prod --show-operation
```

---
## Q32. Explain automated sync, prune, self-heal, and allow-empty.
**Answer:** Automated sync triggers reconciliation without an operator command. `prune` deletes resources no longer desired; `selfHeal` reverts live drift; `allowEmpty` permits an application to legitimately render zero resources while automated pruning is enabled.
### Detailed explanation
- Enable automated sync only after project scope, repository controls, health checks, and rollback procedures are mature.
- Prune is necessary for complete convergence, but it raises deletion risk if paths, generators, or credentials are wrong.
- Self-heal is valuable for unauthorized changes but can conflict with controllers that legitimately mutate fields.
- `allowEmpty` should be rare and intentional; otherwise an empty render should protect against mass deletion.
### Example / commands

```yaml
syncPolicy:
  automated:
    enabled: true
    prune: true
    selfHeal: true
    allowEmpty: false
  retry:
    limit: 5
    backoff:
      duration: 10s
      factor: 2
      maxDuration: 3m
```

---
## Q33. What are sync phases and resource hooks?
**Answer:** Hooks are Kubernetes resources annotated to execute at defined lifecycle phases: `PreSync`, `Sync`, `PostSync`, `SyncFail`, and `PostDelete` where supported. They are commonly used for migrations, smoke tests, and cleanup.
### Detailed explanation
- A PreSync migration must be idempotent or safely detect that it already ran.
- Hook deletion policies determine whether successful or failed hook objects remain.
- Hooks do not execute during selective sync, so do not make a selective sync path depend on a skipped migration.
- A hook runs with the permissions of its service account and is part of the application threat model.
### Example / commands

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: payments-db-migrate
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: BeforeHookCreation,HookSucceeded
    argocd.argoproj.io/sync-wave: "-10"
spec:
  template:
    spec:
      serviceAccountName: payments-migrator
      restartPolicy: Never
      containers:
        - name: migrate
          image: registry.example.com/payments-migrate@sha256:...
```
### L3 risks and common mistakes
- Running destructive, non-backward-compatible database changes before confirming the new application can start.
- Using a hook as an unrestricted scripting escape hatch.

---
## Q34. How do sync waves control ordering?
**Answer:** The `argocd.argoproj.io/sync-wave` annotation assigns an integer ordering value. Lower values run first; negative waves are valid. Argo CD processes the first out-of-sync/unhealthy wave, waits, then continues.
### Detailed explanation
- Use waves for dependencies that cannot be expressed through Kubernetes readiness alone, such as CRDs before CRs or a migration before workloads.
- Keep wave schemes simple and documented; too many waves create fragile procedural deployments.
- Resources within the same wave are still ordered by kind and name, not by a fully custom dependency graph.
- Combine waves with correct readiness probes and health checks.
### Example / commands

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-wave: "5"
```
### L3 risks and common mistakes
- Using arbitrary wave numbers instead of solving missing readiness or controller behavior.
- Assuming a resource being applied means it is operationally ready.

---
## Q35. What are sync windows?
**Answer:** Sync windows are AppProject rules that allow or deny sync operations during schedules for selected applications, namespaces, or clusters. They are governance controls, not a substitute for application safety.
### Detailed explanation
- Use deny windows for protected business periods and allow windows for formal change windows.
- Decide whether manual sync can override a window and restrict the override permission.
- Account for timezone and daylight-saving behavior in globally operated systems.
- Emergency operations need a break-glass process rather than ad hoc policy editing.
### Example / commands

```yaml
spec:
  syncWindows:
    - kind: deny
      schedule: "0 9 * * 1-5"
      duration: 8h
      timeZone: Asia/Kolkata
      applications:
        - "*-prod"
      manualSync: false
```

---
## Q36. How does pruning work, and how do you make it safe?
**Answer:** Pruning deletes tracked live resources that are no longer present in desired state. Make it safe with protected repositories, previewed diffs, correct tracking, project restrictions, explicit deletion confirmation for sensitive resources, and staged rollout.
### Detailed explanation
- Use `PruneLast=true` when deleting old resources only after replacement resources are healthy.
- Use the `Prune=confirm` annotation for high-impact objects that require explicit approval.
- Use `Prune=false` sparingly for resources intentionally retained outside the lifecycle.
- Review propagation policy and finalizers for namespaces, CRDs, PVCs, and operators.
### Example / commands

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Prune=confirm
```

```bash
argocd app diff payments-prod
argocd app sync payments-prod --prune
```
### L3 risks and common mistakes
- A wrong source path that renders no manifests can become a mass-deletion event.
- Pruning a CRD can delete all custom resources managed by that CRD.

---
## Q37. What is server-side apply, and when should Argo CD use it?
**Answer:** Server-side apply sends declarative field intent to the API server, which tracks field ownership in managed fields. It is useful for large resources, shared ownership, and objects that exceed client-side annotation limits, but ownership conflicts must be understood.
### Detailed explanation
- Enable with `ServerSideApply=true` at Application or resource level.
- The field manager determines ownership and conflict behavior.
- SSA can cooperate better with other controllers when each owns distinct fields, but it can also expose previously hidden ownership conflicts.
- Test upgrades because Kubernetes and Argo CD diff/apply behavior can change around managed fields.
### Example / commands

```yaml
spec:
  syncPolicy:
    syncOptions:
      - ServerSideApply=true
```
### L3 risks and common mistakes
- Combining `Replace=true` and SSA without understanding precedence.
- Forcing conflicts can steal fields from another legitimate controller.

---
## Q38. What is the difference between apply, replace, and force sync behavior?
**Answer:** Normal apply patches toward desired state; replace uses create/replace semantics and may recreate objects when immutable fields change; force permits delete/recreate behavior for selected resources. Increasing force increases outage and data-loss risk.
### Detailed explanation
- Use normal apply as the default.
- Use replace for oversized resources or cases where apply semantics are unsuitable, after testing object-specific behavior.
- Use force primarily for repeatable Jobs or exceptional immutable-field changes.
- Never use force casually on StatefulSets, Services with allocated values, PVC-related objects, or CRDs.
### Example / commands

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-options: Force=true,Replace=true
```
### L3 risks and common mistakes
- A successful sync can still cause an avoidable outage if replace recreates a critical object.
- Force does not make an unsafe migration safe.

---
## Q39. How do you customize diff behavior without hiding real drift?
**Answer:** Start by identifying the exact field and its legitimate manager, then ignore only that field for the narrowest resource scope. Prefer JSON pointers, JQ expressions, or trusted managed-field managers with written justification and tests.
### Detailed explanation
- Inspect the raw live object, desired render, and Kubernetes managed fields.
- Determine whether defaulting, mutation, reordering, numeric formatting, or controller behavior causes the difference.
- Configure application-level rules for exceptional cases and system-level rules only for well-understood global behavior.
- Review ignore rules periodically because controller ownership can change after upgrades.
### Example / commands

```bash
argocd app diff APP --local ./rendered
kubectl get RESOURCE NAME -n NS -o yaml --show-managed-fields
argocd app manifests APP > /tmp/desired.yaml
```
### L3 risks and common mistakes
- Ignoring `/spec/template` or an entire resource eliminates meaningful drift detection.
- Ignoring all fields managed by a common manager name may hide malicious or unintended changes.

---
## Q40. How do custom health checks work for CRDs?
**Answer:** Argo CD needs resource-specific logic to map a custom resource's status into `Healthy`, `Progressing`, `Degraded`, or another health state. Custom Lua health checks can be configured when built-in logic is insufficient.
### Detailed explanation
- Base health on stable, documented conditions such as `Ready=True` and observed generation matching metadata generation.
- Distinguish progressing from degraded; a slow reconciliation should not immediately become a failure.
- Include tests for missing status, deletion, paused state, and controller errors.
- Treat custom health code as production logic because it gates later waves and automated processes.
### Example / commands

```lua
hs = {}
if obj.status ~= nil and obj.status.conditions ~= nil then
  for _, c in ipairs(obj.status.conditions) do
    if c.type == "Ready" and c.status == "True" then
      hs.status = "Healthy"
      hs.message = c.message or "Ready"
      return hs
    end
  end
end
hs.status = "Progressing"
hs.message = "Waiting for Ready condition"
return hs
```

---

# 5. Helm, Kustomize, Jsonnet, Multiple Sources, and Plugins

## Q41. How does Argo CD deploy Helm charts?
**Answer:** Argo CD uses Helm primarily as a manifest renderer. It runs a Helm template operation with the configured chart, version, values, and parameters, then Argo CD—not Helm's release controller—tracks and applies the Kubernetes resources.
### Detailed explanation
- A Helm chart can come from a Git repository, Helm repository, or supported OCI registry.
- Values can be supplied through files, inline values, `valuesObject`, parameters, and file parameters with a defined precedence.
- Pin chart versions and dependency versions for reproducibility.
- Helm hooks and Argo CD hooks have different semantics; normalize or disable chart hooks when necessary.
### Example / commands

```yaml
source:
  repoURL: https://charts.example.com
  chart: payments
  targetRevision: 4.8.2
  helm:
    releaseName: payments
    valueFiles:
      - values-prod.yaml
    valuesObject:
      replicaCount: 6
```
### L3 risks and common mistakes
- Assuming `helm list` will show an Argo CD-rendered application as a Helm release.
- Using mutable chart versions or overwriting an existing chart version in the registry.

---
## Q42. Explain Helm value precedence in Argo CD.
**Answer:** The effective value is determined by the configured sources and Helm's precedence. In practice, explicit parameters override inline values or values objects, which override value files, which override chart defaults. Verify the exact installed-version behavior and inspect rendered manifests.
### Detailed explanation
- Avoid configuring the same key in many layers because reviewers cannot easily determine the final value.
- Use value files for coherent environment configuration and parameters for a small number of deliberate overrides.
- For multi-source Applications, value files can come from a separate Git source using a source reference.
- Never place plaintext production secrets in values files.
### Validation
```bash
argocd app manifests APP > /tmp/rendered.yaml
helm template payments ./chart -f values-prod.yaml > /tmp/local.yaml
diff -u /tmp/local.yaml /tmp/rendered.yaml
```

---
## Q43. How does Argo CD use Kustomize?
**Answer:** Argo CD detects a `kustomization.yaml`, executes the configured Kustomize version, and applies the rendered output. It supports images, replicas, common metadata, components, Helm integration, and version selection according to configuration.
### Detailed explanation
- Use bases/components for reusable configuration and overlays for environment differences.
- Pin remote bases to immutable commits or tags; an unpinned remote base breaks reproducibility.
- Test the exact Kustomize version bundled or configured in repo-server.
- Avoid patches that depend on unstable generated names or field ordering.
### Example / commands

```yaml
source:
  repoURL: ssh://git@git.example.com/platform/env-config.git
  targetRevision: 28f4d9c
  path: environments/prod/payments
  kustomize:
    images:
      - registry.example.com/payments@sha256:...
```

---
## Q44. What are multiple-source Applications, and when are they appropriate?
**Answer:** A multiple-source Application combines manifests from more than one source into a single desired state. A common use is a third-party Helm chart plus organization-owned values in Git.
### Detailed explanation
- Sources are rendered independently and their manifests are combined.
- A later source can override a resource with the same group, kind, namespace, and name, but repeated-resource warnings must be reviewed.
- Use multiple sources for a small, tightly related set—not as a replacement for ApplicationSet or application composition across many services.
- Every source expands the availability, credential, provenance, and audit surface.
### Example / commands

```yaml
spec:
  sources:
    - repoURL: https://prometheus-community.github.io/helm-charts
      chart: prometheus
      targetRevision: 27.20.0
      helm:
        valueFiles:
          - $values/monitoring/prod-values.yaml
    - repoURL: ssh://git@git.example.com/platform/values.git
      targetRevision: 5a1c9b7
      ref: values
```
### L3 risks and common mistakes
- Combining unrelated applications into one Application makes health and rollback coarse-grained.
- An overridden resource can conceal an upstream chart change.

---
## Q45. What is Jsonnet support used for?
**Answer:** Jsonnet is a data-templating language that can generate Kubernetes objects with functions, composition, and external variables. Argo CD evaluates `.jsonnet` content and parses the generated objects.
### Detailed explanation
- Use Jsonnet when the organization has expertise and a strong reason for programmable generation.
- Keep evaluation deterministic and pin imported libraries.
- Validate generated object count and schemas in CI because powerful templates can create a large blast radius.
- Do not pass secrets through external variables that become visible in configuration or logs.

---
## Q46. What is a Config Management Plugin (CMP)?
**Answer:** A CMP allows repo-server to run custom discovery, initialization, and manifest-generation logic when built-in Helm, Kustomize, Jsonnet, or directory support is insufficient.
### Detailed explanation
- Modern CMPs run as sidecars associated with repo-server and communicate through the plugin mechanism.
- The plugin image, binary versions, network access, filesystem access, and environment variables are part of the software supply chain.
- Use explicit discovery rules and tar exclusions to avoid copying unnecessary repository content.
- Set timeouts and resource limits because plugin commands can hang or exhaust memory/disk.
### Example / commands

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ConfigManagementPlugin
metadata:
  name: internal-renderer
spec:
  version: v1.2
  generate:
    command: ["/usr/local/bin/render"]
    args: ["--path", "."]
  discover:
    fileName: "platform.yaml"
```
### L3 risks and common mistakes
- A plugin executes repository-controlled input and can become a code-execution path in repo-server.
- Using `curl | sh` during manifest generation destroys reproducibility and increases supply-chain risk.

---
## Q47. How do you secure manifest generation?
**Answer:** Treat repository content as untrusted input and repo-server as a privileged build service. Pin tooling, isolate plugins, minimize network and filesystem access, verify source identity, and prevent secrets from being exposed to template code.
### Detailed explanation
- Run non-root where supported, use read-only filesystems, seccomp, dropped capabilities, and constrained service accounts.
- Separate high-trust and low-trust repositories into distinct Argo CD instances or repo-server pools when the threat model requires it.
- Avoid making cluster credentials available to repo-server; it should generate manifests, not deploy them.
- Scan and sign plugin images and record their exact versions.

---
## Q48. How do Git submodules, Helm dependencies, and remote Kustomize bases affect reliability?
**Answer:** They introduce transitive dependencies that may change or become unavailable independently of the main repository. Every transitive source must be authenticated, pinned, reachable, and included in provenance and recovery planning.
### Detailed explanation
- Pin submodule commits and chart dependency versions.
- Vendor critical dependencies when external availability cannot meet deployment requirements.
- Configure credentials for all private dependencies without over-broad URL templates.
- Test cold-cache rendering; a warm repo-server cache can hide a dependency outage.

---
## Q49. How should OCI artifacts be used with Argo CD?
**Answer:** Use OCI registries for immutable Helm charts or supported manifest artifacts where registry governance, signing, retention, and regional availability are stronger than an ad hoc chart server.
### Detailed explanation
- Pin immutable versions or digests and configure read-only registry credentials.
- Verify signature and provenance outside or within the approved admission workflow.
- Ensure retention policies do not delete artifacts still referenced by production Git revisions.
- Mirror critical public artifacts into a controlled internal registry.

---
## Q50. How do you troubleshoot manifest-generation errors?
**Answer:** Reproduce the exact source revision and rendering tool version, inspect repo-server logs, validate credentials and CA trust, check `/tmp` capacity and memory, and run the same renderer locally in a clean environment.
### Detailed explanation
- Distinguish repository fetch errors from renderer errors and invalid Kubernetes YAML.
- Check command timeout, plugin exit code, missing binary, dependency fetch, and unsupported API versions.
- For monorepos, determine whether concurrent generation and repository locks are contributing.
- Capture the Application condition message and repo-server correlation data before restarting pods.
### Example / commands

```bash
argocd app get APP --hard-refresh
argocd app manifests APP
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd top pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd exec deploy/argocd-repo-server -- df -h /tmp
```

---

# 6. ApplicationSet, Multi-Cluster, and Fleet Management

## Q51. What problem does ApplicationSet solve?
**Answer:** ApplicationSet generates and maintains many Argo CD Applications from a template and one or more generators. It reduces repetitive YAML and enables fleet, tenant, repository, directory, pull-request, and matrix-driven deployment patterns.
### Detailed explanation
- The generator produces parameter sets; the template renders each parameter set into an Application.
- ApplicationSet owns generated Applications and can update or delete them when generator output changes, subject to modification policy.
- It is ideal for 'same application across many clusters' and 'one Application per directory/repository' patterns.
- Because it creates Applications, its source and template are security-sensitive.
### Example / commands

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: payments-fleet
  namespace: argocd
spec:
  generators:
    - clusters:
        selector:
          matchLabels:
            environment: prod
  template:
    metadata:
      name: 'payments-{{name}}'
    spec:
      project: payments
      source:
        repoURL: ssh://git@git.example.com/payments/env-config.git
        targetRevision: 8c21f8a
        path: 'clusters/{{name}}/payments'
      destination:
        server: '{{server}}'
        namespace: payments
```

---
## Q52. Explain the main ApplicationSet generators.
**Answer:** Common generators include List, Cluster, Git directory, Git file, SCM provider, Pull Request, Cluster Decision Resource, Matrix, Merge, and Plugin generators. Select the smallest generator that directly represents the business inventory.
### Detailed explanation
- List is explicit and predictable for a small fixed fleet.
- Cluster uses Argo CD cluster secrets and labels as inventory.
- Git directory/file derives Applications from repository structure or structured files.
- SCM provider and Pull Request generators discover repositories or temporary review environments.
- Matrix creates combinations; Merge overlays parameter sets by merge keys.
- Plugin generators call external logic and therefore need stronger reliability and security controls.
### L3 risks and common mistakes
- A broad SCM generator can accidentally onboard every repository in an organization.
- Matrix cardinality can grow unexpectedly and generate thousands of Applications.

---
## Q53. How does the Cluster generator discover clusters?
**Answer:** It reads Argo CD cluster Secrets and exposes fields such as cluster name, server, labels, and annotations as template parameters. Label selectors determine which clusters are included.
### Detailed explanation
- Treat cluster-secret labels as fleet inventory and govern who can change them.
- Use labels for environment, region, cloud, compliance class, business unit, and deployment ring.
- Validate that the in-cluster secret and remote-cluster secrets use consistent naming and labels.
- A label removal can cause a generated Application to be deleted, so combine selectors with safe ApplicationSet sync policy.
### Example / commands

```bash
kubectl -n argocd get secret \
  -l argocd.argoproj.io/secret-type=cluster \
  -o custom-columns=NAME:.metadata.name,CLUSTER:.data.name,LABELS:.metadata.labels
```

---
## Q54. What are Matrix and Merge generators?
**Answer:** Matrix creates a Cartesian product of parameters from child generators; Merge combines matching parameter sets and overlays values using merge keys.
### Detailed explanation
- Matrix is useful for every application across every selected cluster or every environment across every region.
- Merge is useful when a base cluster inventory is overridden by environment- or cluster-specific settings.
- Estimate the generated count before applying and use preview tooling.
- Use unique, stable Application names that include the required dimensions without exceeding Kubernetes name limits.
### L3 risks and common mistakes
- Matrix can create an explosive number of Applications and repository requests.
- Ambiguous merge keys can silently apply the wrong overrides.

---
## Q55. How do you prevent ApplicationSet from unexpectedly deleting Applications?
**Answer:** Use ApplicationSet modification policies such as create-only or create-update where appropriate, protect deletion with finalizer and controller settings, stage generator changes, and preview generated Applications before merge.
### Detailed explanation
- `applicationsSync: create-only` prevents updates and deletion by the controller.
- `create-update` permits updates but prevents deletion.
- Preserved fields and ignore-difference controls can prevent selected generated fields from being overwritten, but must not create policy bypasses.
- For a cluster offboarding workflow, deliberately remove workload Applications only after data and service transition checks pass.
### Example / commands

```yaml
spec:
  syncPolicy:
    applicationsSync: create-update
    preserveResourcesOnDeletion: true
```
### L3 risks and common mistakes
- Assuming an ApplicationSet deletion cannot affect workloads; generated Applications may contain resource finalizers.
- Changing a Git directory naming convention can make generator output disappear.

---
## Q56. What are Progressive Syncs in ApplicationSet?
**Answer:** Progressive Syncs control the order and batching of updates to Applications generated by an ApplicationSet. They can reduce blast radius by rolling changes through groups, but the capability is beta in the current documentation and requires explicit enablement and testing.
### Detailed explanation
- A RollingSync strategy selects Applications by labels and applies step-by-step limits.
- Use deployment rings such as canary, non-critical production, and critical production.
- Define clear success and failure gates; health logic must be reliable because rollout progression depends on it.
- Do not treat beta progressive sync as a complete progressive-delivery platform for traffic shifting or metric analysis.
### L3 risks and common mistakes
- Using `maxUpdate: 100%` removes the intended blast-radius reduction.
- Incorrect labels can skip a critical ring or include too many clusters.

---
## Q57. ApplicationSet versus App-of-Apps—how do you choose?
**Answer:** Choose ApplicationSet for parameterized, generated, and fleet-oriented Application management. Choose App-of-Apps for a small, explicit hierarchy or bootstrap bundle where each child is intentionally listed.
### Detailed explanation
- ApplicationSet provides generators, selectors, templating, and modification policies.
- App-of-Apps uses ordinary manifest rendering and can be simpler for a static platform bootstrap.
- Both can be privileged because they create Applications.
- Avoid mixing multiple ownership models for the same child Application.

---
## Q58. How would you design Argo CD for hundreds of clusters?
**Answer:** Use clear fleet inventory, shard reconciliation, partition trust domains, standardize cluster labels and Projects, automate credential lifecycle, and monitor API, repository, queue, cache, and controller performance.
### Detailed explanation
- Decide between centralized, regional, or per-cluster Argo CD based on network reachability, regulatory isolation, failure domain, latency, and operational ownership.
- Centralized management simplifies policy but creates a large credential and availability blast radius.
- Regional instances reduce latency and blast radius while retaining platform consistency.
- Per-cluster instances maximize isolation but increase upgrade and observability overhead.
- Use ApplicationSet or agent-based architectures only after validating version support and operational maturity.

---
## Q59. How do you onboard and offboard a cluster safely?
**Answer:** Onboarding requires identity, connectivity, TLS trust, scoped authorization, inventory labels, project destinations, a canary application, monitoring, and documented ownership. Offboarding reverses these in a controlled order after workloads and data are migrated.
### Detailed explanation
- Register a least-privilege service account and verify API reachability from controller pods.
- Apply labels only after cluster readiness checks pass so generators do not deploy prematurely.
- For offboarding, stop new promotion, remove the cluster from generators, decide whether to prune or preserve applications, rotate credentials, and remove project destinations.
- Retain audit evidence and verify no Application still targets the cluster.
### Example / commands

```bash
argocd cluster list
argocd app list -o json | jq -r '.[] | select(.spec.destination.name=="CLUSTER_NAME") | .metadata.name'
kubectl -n argocd get applications.argoproj.io \
  -o json | jq -r '.items[] | select(.spec.destination.name=="CLUSTER_NAME") | .metadata.name'
```

---
## Q60. How do you handle temporary pull-request environments?
**Answer:** Use the Pull Request generator or CI-created configuration to generate short-lived Applications with isolated namespaces, quotas, network policies, expiry controls, and safe deletion.
### Detailed explanation
- Do not expose production secrets or privileged service accounts to preview environments.
- Sanitize branch and PR values used in names and template fields.
- Set TTL or cleanup automation independent of a webhook so abandoned PRs do not leak resources.
- Restrict source repositories and destination namespaces in a dedicated AppProject.
### L3 risks and common mistakes
- Executing untrusted fork code with internal credentials.
- Allowing a PR template to choose arbitrary targetRevision, repository URL, Project, or cluster.

---

# 7. Security, RBAC, SSO, Secrets, and Governance

## Q61. How does Argo CD RBAC work?
**Answer:** Argo CD uses a policy model based on subjects, resources, actions, objects, and allow/deny effects. Policies and group mappings are configured in `argocd-rbac-cm`, while AppProject roles can define project-scoped permissions.
### Detailed explanation
- Start with `policy.default: role:readonly` or a more restrictive custom default, not broad authenticated-user access.
- Map SSO groups to roles and avoid individual user policies except for exceptional cases.
- Restrict sensitive actions such as application override, project update, repository management, cluster management, exec, and extension invocation.
- Test policy with `argocd admin settings rbac can` for representative users and groups before rollout.
### Example / commands

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:payments-operator, applications, get, payments/*, allow
    p, role:payments-operator, applications, sync, payments/*, allow
    p, role:payments-operator, logs, get, payments/*, allow
    g, payments-sre, role:payments-operator
  scopes: '[groups, email]'
```
### L3 risks and common mistakes
- Using broad wildcard objects such as `*/*` without understanding project/application matching.
- Granting `applications, update` lets users modify Application specs and potentially bypass Git-controlled intent.

---
## Q62. How do AppProject roles differ from global RBAC roles?
**Answer:** Global RBAC covers Argo CD-wide resources and cross-project policy. AppProject roles are defined inside a Project and are naturally scoped to applications in that project, with optional group mappings and JWT tokens.
### Detailed explanation
- Use project roles for service/team operations that should not affect repositories, clusters, or other Projects.
- Use global roles for platform administrators and cross-project personas.
- Avoid long-lived project JWTs where OIDC workload identity or short-lived tokens are available.
- Revocation, expiry, and token inventory must be part of the credential-management process.

---
## Q63. How do you integrate Argo CD with enterprise SSO?
**Answer:** Configure OIDC directly or use Dex connectors, register the correct callback URL, map stable group claims, enforce MFA at the identity provider, and map groups to least-privilege RBAC roles.
### Detailed explanation
- Use immutable group identifiers when display names can change or collide.
- Validate token size, group overage behavior, refresh-token behavior, and session expiry.
- Keep at least one tested emergency local-access procedure, but disable routine use of the admin account.
- Monitor OIDC discovery, JWKS rotation, certificate validity, and clock skew.
### Example / commands

```yaml
data:
  url: https://argocd.example.com
  oidc.config: |
    name: Corporate-SSO
    issuer: https://id.example.com/realms/platform
    clientID: argocd
    clientSecret: $oidc.clientSecret
    requestedScopes: ["openid", "profile", "email", "groups"]
```
### L3 risks and common mistakes
- Mapping access based only on mutable email addresses.
- Group claims containing thousands of groups can exceed proxy or cookie limits.

---
## Q64. How do you disable or control the built-in admin account?
**Answer:** After SSO and emergency access are validated, disable the built-in admin account for routine operation and protect any remaining local accounts with strong credentials, minimal capabilities, and audit monitoring.
### Detailed explanation
- Store initial credentials securely and rotate after bootstrap.
- Set `admin.enabled: 'false'` in the supported configuration mechanism when the recovery design permits it.
- Do not share a local admin credential among operators; it destroys individual accountability.
- Document how access is restored if the identity provider is unavailable.

---
## Q65. How should secrets be handled in GitOps?
**Answer:** Do not commit plaintext secrets. Use an approved model such as External Secrets Operator, Secrets Store CSI Driver, Vault integration, SOPS-encrypted files, or Sealed Secrets, and define where decryption occurs and who can access plaintext.
### Detailed explanation
- External reference models keep secret values outside Git and store only references in manifests.
- Encrypted-in-Git models require key lifecycle, access control, rotation, and compromise recovery.
- Repo-server plugins that decrypt secrets expose plaintext in the manifest-generation boundary; isolate and audit them.
- Prevent rendered manifests and diffs containing secrets from being broadly visible through UI, API, logs, and caches.
### L3 risks and common mistakes
- Base64 is encoding, not encryption.
- A secret manager does not help if the workload service account can read every secret in the cluster.

---
## Q66. How do you protect repository credentials and private CAs?
**Answer:** Use namespace-level least privilege, encryption at rest, external secret synchronization, short-lived credentials where possible, read-only access, rotation, and monitored changes to repository and CA configuration.
### Detailed explanation
- Separate repository read credentials from any write-back automation credentials.
- Use repository-specific deploy keys instead of organization-wide tokens.
- Manage CA bundles and SSH host keys declaratively through protected platform configuration.
- Alert on secret changes and failed authentication spikes.

---
## Q67. What is GnuPG verification in Argo CD?
**Answer:** GnuPG verification can verify signed Git commits or tags used as the application source, helping establish that a trusted key signed the revision before deployment.
### Detailed explanation
- Verification strengthens source authenticity but does not replace branch protection, code review, or artifact signing.
- Protect and rotate trusted public keys and document revocation handling.
- Define whether production tracks signed commits, signed tags, or both.
- Ensure automation identities can sign in an auditable and protected manner.

---
## Q68. How do admission policies complement Argo CD?
**Answer:** Argo CD controls desired-state delivery; admission controls such as ValidatingAdmissionPolicy, Kyverno, Gatekeeper, or platform policy validate whether a submitted object is allowed. Together they provide layered control.
### Detailed explanation
- Run the same policies or equivalent checks in pull-request CI to fail before merge.
- Keep the API-server admission policy authoritative because Argo CD is not the only possible writer.
- Argo CD should report admission failures clearly; alert on repeated policy-denied syncs.
- Design exceptions as explicit, time-bound, approved resources rather than disabling policy globally.

---
## Q69. How do you restrict dangerous Kubernetes resources?
**Answer:** Use AppProject resource allow/deny lists, scoped target-cluster RBAC, admission policy, and separate platform Projects. No single layer is sufficient.
### Detailed explanation
- Application teams usually should not create ClusterRoles, ClusterRoleBindings, CRDs, admission webhooks, APIService objects, or privileged namespaces.
- Namespace-scoped RoleBindings can still escalate if they bind a powerful ClusterRole, so admission and API RBAC must validate subjects and roleRefs.
- Restrict custom resources whose operators perform privileged actions.
- Review `resource.customizations`, resource actions, and terminal/exec capabilities as additional attack surface.

---
## Q70. What audit evidence should a production GitOps platform retain?
**Answer:** Retain the source commit and signature, pull-request review and checks, image digest and provenance, Argo CD operation history, identity of manual operations, Kubernetes audit events, policy decisions, and notification/incident records.
### Detailed explanation
- Correlate events with application name, project, cluster, namespace, Git SHA, and operation ID.
- Export logs and metrics to centralized storage because pod-local logs are ephemeral.
- Set retention according to regulatory and incident-response requirements.
- Periodically prove that a running workload can be traced back to an approved commit and artifact.

---

# 8. High Availability, Scale, Performance, and Observability

## Q71. How do you scale the application-controller?
**Answer:** Scale by sharding clusters and tuning controller processors, cache behavior, Kubernetes API usage, and resource limits. The exact method depends on the installed Argo CD version and HA topology.
### Detailed explanation
- Measure workqueue depth, reconciliation latency, Kubernetes request rate, memory, and CPU before adding replicas.
- Distribute cluster assignments evenly and verify shard ownership after changes.
- A controller can be bottlenecked by target API latency rather than local CPU.
- Avoid aggressive processor counts that overwhelm target clusters or Redis.
### Example / commands

```bash
kubectl -n argocd top pod -l app.kubernetes.io/name=argocd-application-controller
kubectl -n argocd logs statefulset/argocd-application-controller | grep -i shard
kubectl -n argocd get cm argocd-cmd-params-cm -o yaml
```

---
## Q72. How do you scale repo-server?
**Answer:** Scale repo-server replicas and parallelism based on render concurrency, repository size, tool memory, disk usage, and cache behavior. Separate expensive or untrusted plugins when necessary.
### Detailed explanation
- Repo-server clones repositories under temporary storage and forks render tools; memory and ephemeral storage are common bottlenecks.
- Use the parallelism limit to prevent OOM and thread exhaustion.
- A monorepo can serialize or repeat expensive work; optimize path-based refresh and repository design.
- Mounting persistent storage can reduce repeated data movement in some designs but changes security and failure considerations.
### Example / commands

```bash
kubectl -n argocd top pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server | egrep -i 'timeout|oom|fork|thread|disk'
```

---
## Q73. What causes high reconciliation latency?
**Answer:** Common causes are large application counts, slow target APIs, expensive manifest generation, repository polling, Redis latency, excessive diff scope, API throttling, failing discovery, and controller under-sizing.
### Detailed explanation
- Break latency into source fetch, manifest generation, live-state query, comparison, queue wait, apply, and health wait.
- Compare a single affected cluster/application with platform-wide symptoms.
- Check Kubernetes client throttling messages and target API server metrics.
- Use refresh webhooks to reduce normal detection latency but do not hide an overloaded controller.

---
## Q74. What metrics should be monitored?
**Answer:** Monitor component availability, application sync/health counts, reconciliation duration, operation duration and failures, queue depth, repository request errors, cache/Redis behavior, Kubernetes API requests, resource usage, and notification delivery.
### Detailed explanation
- Alert on symptoms that require action: sustained reconciliation delay, high failure rate, no successful reconciliations, repository authentication failures, cluster-unreachable status, and widespread `Unknown` health.
- Use labels carefully; per-application metrics can create high Prometheus cardinality.
- Combine metrics with logs and Application conditions for root cause.
- Create SLOs for detection latency, reconciliation success, API availability, and recovery—not only pod uptime.

---
## Q75. How do you design Prometheus alerts for Argo CD?
**Answer:** Use multi-window or sustained alerts that distinguish one application failure from platform-wide failure and include project, cluster, application, and revision context.
### Detailed explanation
- Page for controller outage, fleet-wide repository failure, or critical production drift that cannot reconcile.
- Create tickets or chat alerts for non-critical application degradation and credential-expiry trends.
- Suppress expected maintenance windows carefully; do not suppress actual failed upgrades.
- Alert when metrics disappear, because silent exporter failure can look healthy.
### Example / commands

```promql
sum by (project) (argocd_app_info{health_status="Degraded"}) > 0
```

Metric names and labels can vary by version; verify them from the `/metrics` endpoint of the installed release.

---
## Q76. How do Argo CD Notifications work?
**Answer:** The Notifications controller evaluates triggers against Application state, renders templates, and sends messages through configured services such as email, chat, webhooks, or incident systems.
### Detailed explanation
- Use reusable trigger and template catalogs but review them before installation.
- Subscriptions can be global, project-based, or application annotations depending on configuration.
- Protect service credentials in Secrets and restrict self-service notification configuration if templates can send sensitive data.
- Deduplicate repeated alerts and include actionable links, revision, cluster, sync result, and failure message.

---
## Q77. What is orphaned-resource monitoring?
**Answer:** Orphaned-resource monitoring identifies top-level namespaced resources in an application's destination namespace that are not managed by any Argo CD Application. It helps detect configuration drift and unmanaged objects.
### Detailed explanation
- Enable warnings in AppProject configuration after understanding normal shared resources.
- Use ignore rules for known platform-created resources rather than disabling the feature.
- An orphan is not automatically safe to delete; determine its owner and creation history.
- Cluster-scoped resources and nested controller-created objects require separate analysis.

---
## Q78. How do you reduce Prometheus metric cardinality?
**Answer:** Limit per-application labels, avoid exporting unconstrained Git metadata, aggregate dashboards by project/cluster, and retain detailed per-application state in Argo CD/API logs rather than every metric label.
### Detailed explanation
- High application counts multiplied by revision, repository, destination, and custom labels can create millions of series.
- Review `argocd_app_labels` and metric label configuration before exposing arbitrary user labels.
- Use recording rules for operational summaries.
- Measure TSDB series count and scrape size after onboarding large fleets.

---
## Q79. How do you tune monorepo performance?
**Answer:** Reduce unnecessary refresh and rendering by using clear application paths, path-sensitive webhook behavior, manifest-generation path annotations where appropriate, pinned dependencies, and repositories without large unrelated binaries.
### Detailed explanation
- Measure clone/fetch time, generation time, cache hit rate, and lock contention.
- Use `.gitignore` and plugin tar exclusions but remember Git history and LFS still affect fetch behavior.
- Split the repository when access control or rendering contention becomes the dominant problem.
- Do not use shallow or partial approaches without testing tag and revision resolution.

---
## Q80. How do you perform capacity planning for Argo CD?
**Answer:** Model the number of Applications, resources per Application, clusters, refresh frequency, repository size, render cost, sync rate, notification volume, and failure bursts. Test with production-like manifests and API latency.
### Detailed explanation
- Average load is insufficient; a global Git change or cluster reconnect can cause a reconciliation storm.
- Include repo-server ephemeral storage and memory, controller cache memory, Redis limits, API server QPS, and Prometheus cardinality.
- Define headroom for upgrades, node failure, and simultaneous incident diagnostics.
- Document scaling triggers and verify they actually reduce the measured bottleneck.

---

# 9. Operations, Backup, Disaster Recovery, Upgrade, and OpenShift GitOps

## Q81. What must be backed up for Argo CD disaster recovery?
**Answer:** Back up declarative configuration and required Secrets: Applications, AppProjects, ApplicationSets, repository and cluster credentials, Argo CD ConfigMaps/Secrets, notification settings, trusted CAs/host keys, and any Operator CRs. Git repositories and external identity/secret systems require their own recovery plans.
### Detailed explanation
- Use the supported `argocd admin export`/`import` workflow for version-compatible Argo CD configuration where applicable.
- Encrypt backups, separate backup credentials, and protect backups from namespace or cluster compromise.
- Record the Argo CD version and CRD version used to create the export.
- Test restoration into an isolated environment and confirm Applications can render without automatically impacting production.
### Example / commands

```bash
argocd admin export > argocd-export-$(date +%F).yaml
# Encrypt and transfer to protected storage.

argocd admin import - < argocd-export-YYYY-MM-DD.yaml
```
### L3 risks and common mistakes
- Backing up only Git. Cluster and repository credentials, RBAC, SSO configuration, and Project policy may not be recoverable from application repos.
- Restoring an old cluster credential without rotating it can reintroduce compromised access.

---
## Q82. Describe an Argo CD disaster-recovery procedure.
**Answer:** Rebuild a compatible Kubernetes/OpenShift management environment, install the pinned Argo CD version and CRDs, restore configuration and credentials, validate access in read-only or paused mode, then progressively resume reconciliation.
### Detailed explanation
- Confirm DNS, ingress/Route, TLS, SSO, Git, registry, secret manager, Redis, and target API connectivity.
- Prevent accidental mass prune during validation by disabling automated sync or using a controlled restoration network boundary.
- Compare generated desired state and live resources before resuming self-heal and prune.
- Restore critical Projects and applications first, then lower-priority fleets.
- After recovery, rotate sensitive credentials and document recovery time and data gaps.

---
## Q83. How do you upgrade Argo CD safely?
**Answer:** Read every intervening upgrade note, back up configuration, validate CRD and API changes, test in a representative non-production instance, pin images/manifests, use server-side apply, monitor rollout and behavior, and maintain a tested rollback or forward-fix plan.
### Detailed explanation
- Upgrade one minor step at a time when required by documented migration paths.
- Apply the complete release manifests rather than changing only image tags.
- Review changes to RBAC, diffing, tracking, health, ApplicationSet, cluster secrets, and security defaults.
- Run pre/post checks for rendering, sync, SSO, webhooks, remote clusters, notifications, and metrics.
- Do not upgrade the control plane and every managed application during the same change window.
### Example / commands

```bash
argocd version
argocd admin export > /secure/backup/argocd-pre-upgrade.yaml
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/vX.Y.Z/manifests/ha/install.yaml
kubectl -n argocd get pods
argocd app list
```

---
## Q84. How do you roll back a failed Argo CD upgrade?
**Answer:** First determine whether failure is deployment-level, configuration-level, or data/schema behavior. Roll back only if the previous version is compatible with already-applied CRDs and migrated data; otherwise follow the documented forward-fix path.
### Detailed explanation
- Preserve logs, events, manifests, and current configuration before changing versions again.
- Do not blindly downgrade CRDs; Kubernetes does not automatically reverse stored-version changes.
- Restore the previous complete manifest set and configuration only when supported.
- Keep managed workloads stable by pausing risky automated sync if controller behavior is uncertain.

---
## Q85. How do you rotate Git and cluster credentials without outage?
**Answer:** Stage new credentials alongside old trust where possible, validate with a canary repository or cluster, switch references or secret data, monitor authentication, then revoke the old credential after all consumers converge.
### Detailed explanation
- For TLS or SSH host-key rotation, trust old and new keys during the overlap period.
- For repository tokens, use credential templates carefully and confirm all matching repositories.
- For cluster service accounts, create a new token/identity, update the cluster Secret, verify application-controller access, then remove the old binding.
- Record credential ownership and expiry to prevent emergency rotation.

---
## Q86. How do you handle an Argo CD outage?
**Answer:** First remember that running workloads usually continue; the outage primarily affects reconciliation, visibility, and new deployments. Stabilize the control plane, preserve evidence, classify the failed dependency, and avoid unnecessary workload changes.
### Detailed explanation
- Check namespace events and component health before restarting everything.
- Classify server/UI, repository, controller, Redis, SSO, DNS, Git, or target-cluster failure.
- Use direct Kubernetes access only under a controlled break-glass process and capture any resulting drift.
- After recovery, force refresh critical Applications, inspect missed changes, and reconcile emergency modifications back to Git.

---
## Q87. What is different about Red Hat OpenShift GitOps?
**Answer:** Red Hat OpenShift GitOps packages and supports Argo CD through an Operator and OpenShift-integrated configuration. The Operator manages ArgoCD custom resources, routes, component sizing, RBAC integration, and supported feature configuration.
### Detailed explanation
- Make changes through the `ArgoCD` custom resource instead of directly patching generated Deployments and Services.
- Understand the default instance, namespace scope, cluster configuration, OpenShift OAuth/Route integration, and operator-managed network policies for the installed release.
- Use Red Hat's compatibility and upgrade guidance rather than assuming upstream and downstream versions are identical.
- OpenShift SCC, Routes, Operators, and cluster-scoped resources require OpenShift-specific permission and health analysis.
### Example / commands

```bash
oc get csv -A | grep -i gitops
oc get argocd -A
oc get pods -n openshift-gitops
oc get route -n openshift-gitops
oc logs -n openshift-operators deploy/openshift-gitops-operator-controller-manager
```

---
## Q88. How do you troubleshoot an Operator-managed Argo CD instance?
**Answer:** Inspect the `ArgoCD` custom resource, its status/conditions, Operator logs, generated workloads, events, and any reconciliation errors. Fix the custom resource or dependency rather than editing managed children.
### Detailed explanation
- Confirm the GitOps Operator ClusterServiceVersion and subscription health.
- Check whether unsupported fields are rejected or ignored by the installed CRD.
- Compare desired fields in the CR with generated Deployments, StatefulSets, Routes, Services, and NetworkPolicies.
- A direct edit may temporarily work but will be reverted by the Operator and create configuration drift.

---
## Q89. How should Argo CD be integrated with CI?
**Answer:** CI should test and build immutable artifacts, publish provenance, and propose a GitOps configuration change. Argo CD should reconcile the approved Git state. Use the Argo CD API only for observation or narrowly controlled operations when Git alone is insufficient.
### Detailed explanation
- A common pipeline updates an image digest in the environment repository and opens a pull request.
- Use webhooks to accelerate refresh after merge.
- CI can wait on Argo CD application health for feedback, but avoid giving CI broad update or cluster permissions.
- Avoid direct parameter overrides because the desired state becomes split between Git and Argo CD.
### Example / commands

```bash
# After PR merge, CI may observe deployment without changing the Application:
argocd app wait payments-prod --sync --health --timeout 900
argocd app get payments-prod -o json | \
  jq -e --arg rev "$GITOPS_COMMIT" '.status.sync.revision == $rev'
```

---
## Q90. How do you perform a controlled application rollback?
**Answer:** Prefer reverting or correcting the Git configuration to a known-good immutable artifact and let Argo CD reconcile. Coordinate data-schema compatibility, traffic, and external dependencies before rollback.
### Detailed explanation
- Identify the last known-good Git SHA and image digest from Application history and audit data.
- Create a reviewed revert or forward-fix commit.
- Pause automated promotion if a pipeline would immediately reintroduce the bad version.
- For databases, confirm backward compatibility; application rollback may be impossible after a destructive migration.
- Validate health, error rate, latency, and business transactions—not only sync success.
### Example / commands

```bash
argocd app history payments-prod
# Preferred: git revert <bad-config-commit>; merge reviewed PR
argocd app sync payments-prod --revision <revert-commit>
argocd app wait payments-prod --sync --health --timeout 600
```

---

# 10. Corporate L3 Scenarios and Troubleshooting

## Q91. An Application remains OutOfSync immediately after every successful sync. How do you troubleshoot?
**Answer:** Compare the exact desired and live fields, identify who changes them, and correct ownership or diff normalization rather than repeatedly syncing.
### Detailed explanation
- Run `argocd app diff` and inspect the resource with managed fields.
- Check mutating admission webhooks, operators, HPA/VPA, service mesh injection, defaulting, and list-order behavior.
- Verify that two Applications are not managing the same resource.
- Confirm the renderer is deterministic and does not generate timestamps, random values, or unstable ordering.
- Add a narrowly scoped ignore rule only after proving the field is legitimately owned elsewhere.
### Example / commands

```bash
argocd app diff APP
argocd app get-resource APP --kind KIND --resource-name NAME --show-managed-fields -o yaml
kubectl get KIND NAME -n NS -o yaml --show-managed-fields
kubectl get mutatingwebhookconfigurations
```

---
## Q92. An Application is Synced but Degraded. What does that indicate?
**Answer:** Git and live state match, but the deployed resources are unhealthy. Investigate workload runtime, dependencies, probes, scheduling, quotas, storage, networking, and custom health—not the sync mechanism first.
### Detailed explanation
- Open the resource tree and identify the first degraded object.
- Inspect Deployment/StatefulSet conditions, pod events, logs, endpoints, PVCs, and Jobs.
- Check whether a custom resource health check correctly interprets status.
- A new Git change may be required, but repeated sync of identical manifests will not fix a runtime fault.
### Example / commands

```bash
argocd app resources APP
kubectl -n NS get events --sort-by=.lastTimestamp
kubectl -n NS describe deploy APP
kubectl -n NS logs deploy/APP --all-containers --since=15m
```

---
## Q93. Argo CD reports repository authentication failure after certificate rotation. What do you do?
**Answer:** Determine whether the failure is TLS trust, hostname validation, SSH host key, client credential, proxy, or repository authorization. Update trust safely with overlap, then refresh and validate.
### Detailed explanation
- Test from repo-server's network context and inspect the exact error.
- For TLS, add the issuing CA and verify SAN/expiry; adding a CA cannot fix a hostname mismatch.
- For SSH, update known-host keys and verify the presented key through an independent channel.
- Do not permanently bypass verification.
### Example / commands

```bash
kubectl -n argocd logs deploy/argocd-repo-server --since=20m
argocd cert list --cert-type https
argocd cert list --cert-type ssh
argocd repo get REPO_URL --refresh hard
```

---
## Q94. A sync is stuck in Running or Terminating. How do you diagnose it?
**Answer:** Inspect operation state and per-resource message, identify a hanging hook or resource health wait, check finalizers and API connectivity, then terminate only after understanding partial changes.
### Detailed explanation
- A Job hook may be pending due to quota, scheduling, image pull, or service-account permissions.
- A terminating resource may have a finalizer whose controller is unavailable.
- A target API outage can leave operation state stale until connectivity returns.
- After termination, compare live and desired state; the operation is not automatically transactional.
### Example / commands

```bash
argocd app get APP --show-operation
argocd app terminate-op APP
kubectl -n NS get jobs,pods
kubectl get RESOURCE NAME -o json | jq '.metadata.finalizers'
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
```

---
## Q95. A production namespace was manually changed during an incident and self-heal reverted it. How do you redesign the process?
**Answer:** Keep self-heal as a guardrail but establish a break-glass workflow that coordinates temporary reconciliation suspension, time-bound access, captured commands, approval, validation, and immediate Git back-port.
### Detailed explanation
- During a declared incident, pause auto-sync for the specific Application or use a controlled ignore only if necessary.
- Record the live patch and expected duration.
- Create a corrective Git commit as soon as the safe final state is known.
- Re-enable self-heal, hard-refresh, and verify no unexplained drift remains.
- Review why the emergency control was not represented in a tested runbook.
### L3 risks and common mistakes
- Disabling self-heal globally for all applications.
- Leaving the emergency patch only in the cluster, guaranteeing future drift or loss.

---
## Q96. A Git change accidentally targets every production cluster. How do you limit blast radius?
**Answer:** Stop further reconciliation, assess which Applications have started, revert the Git change, and use fleet segmentation so future changes progress through canary rings rather than all clusters at once.
### Detailed explanation
- Pause the affected ApplicationSet or disable auto-sync at the generated Application level through the supported process.
- Do not delete the ApplicationSet blindly because deletion behavior may cascade.
- Use cluster labels, separate Projects/instances, progressive sync, and change windows to segment rollout.
- Require generated-application preview and count checks in CI.
- After recovery, confirm no cluster remains on the bad revision.

---
## Q97. Repo-server is repeatedly OOMKilled. What is your L3 response?
**Answer:** Preserve evidence, identify the repository/application/render process causing memory growth, reduce concurrency, set realistic requests/limits, optimize the source, and isolate pathological plugins or charts.
### Detailed explanation
- Check pod termination reason, memory graphs, render duration, repository size, and concurrent generation.
- Reproduce the specific application in a controlled container with the same tool versions.
- Tune parallelism to avoid many simultaneous Helm/Kustomize/plugin processes.
- Remove large generated files or binaries from repositories and investigate plugin leaks.
- Scaling replicas helps throughput only if work is distributable and the underlying source is not pathological.
### Example / commands

```bash
kubectl -n argocd describe pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd top pod -l app.kubernetes.io/name=argocd-repo-server
kubectl -n argocd logs deploy/argocd-repo-server --previous
```

---
## Q98. Application-controller CPU is high and reconciliation is delayed. What do you inspect?
**Answer:** Measure queue and reconciliation metrics, identify noisy applications or unreachable clusters, inspect API throttling and diff cost, then tune processors or sharding only after isolating the workload driver.
### Detailed explanation
- A disconnected cluster can cause repeated expensive retries.
- A high-churn operator or frequently changing resource can trigger repeated comparisons.
- A giant Application with thousands of resources can dominate reconciliation.
- Review resource exclusions and ignore rules only for controller-generated resources that truly should not be watched.
- Coordinate client QPS changes with target API capacity.

---
## Q99. An ApplicationSet generated the wrong destination namespace. How do you investigate?
**Answer:** Render or preview generator output, inspect the parameter values and template, compare cluster-secret labels/files, and determine whether Go template missing-key behavior or parameter collision produced the result.
### Detailed explanation
- Check List/Git/Cluster generator output and Matrix/Merge precedence.
- Use `goTemplateOptions: ["missingkey=error"]` where supported so missing values fail rather than silently rendering zero values.
- Validate namespace naming with policy before merge.
- Stop automated sync for affected generated Applications before correcting the template.
### L3 risks and common mistakes
- Deleting generated Applications before understanding whether their finalizers will prune workloads.
- Using a default namespace value that points to a shared or privileged namespace.

---
## Q100. Design a production-grade GitOps platform for a regulated enterprise.
**Answer:** Use layered trust boundaries: protected Git repositories, signed immutable artifacts, regional HA Argo CD instances, SSO/MFA, least-privilege Projects and cluster service accounts, admission policy, external secrets, progressive rollout, central observability, and tested disaster recovery.
### Detailed explanation
- Separate platform, shared-service, and application Projects and repositories.
- Use regional instances or fleet partitions to keep credentials and failure domains bounded.
- Promote immutable image digests through reviewed environment repositories.
- Enforce source/destination/resource restrictions in AppProjects and API-server policy.
- Use default-deny network policy and restrict repo-server egress and plugin execution.
- Track SLOs for reconciliation, deployment success, drift detection, and recovery.
- Test upgrade, Git outage, identity-provider outage, cluster credential rotation, namespace deletion, and restore scenarios.
- Maintain a documented break-glass process that always returns accepted state to Git.
### Example / commands

```text
Developers -> Source PR -> CI build/test/sign -> Immutable registry
                                      |
                                      v
                              Environment PR
                                      |
                     review + policy + signature
                                      |
                                      v
 Regional HA Argo CD -> canary cluster -> production ring 1 -> ring 2
         |                    |               |
         +---- SSO/RBAC ------+---- Admission policy
         +---- Metrics/logs/audit -> SIEM and incident management
         +---- External secrets / KMS / Vault
```

---

# 11. Enterprise Reference Manifests

## 11.1 Restricted AppProject

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: payments
  namespace: argocd
spec:
  description: Payments workloads managed by the Payments SRE team
  sourceRepos:
    - ssh://git@git.example.com/payments/env-config.git
    - oci://registry.example.com/platform/charts
  destinations:
    - name: prod-cluster-01
      namespace: payments-*
    - name: prod-cluster-02
      namespace: payments-*
  clusterResourceWhitelist:
    - group: ""
      kind: Namespace
  clusterResourceBlacklist:
    - group: apiextensions.k8s.io
      kind: CustomResourceDefinition
    - group: admissionregistration.k8s.io
      kind: "*"
    - group: rbac.authorization.k8s.io
      kind: ClusterRoleBinding
  namespaceResourceBlacklist:
    - group: rbac.authorization.k8s.io
      kind: RoleBinding
  orphanedResources:
    warn: true
  syncWindows:
    - kind: deny
      schedule: "0 9 * * 1-5"
      duration: 8h
      timeZone: Asia/Kolkata
      applications:
        - "*-prod"
      manualSync: false
```

## 11.2 Production Application with Retry and Safer Pruning

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: payments-prod-cluster-01
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: payments
  source:
    repoURL: ssh://git@git.example.com/payments/env-config.git
    targetRevision: 8c21f8a71d9d9b6f8c0a0a17f9de000000000000
    path: environments/prod/cluster-01/payments
  destination:
    name: prod-cluster-01
    namespace: payments-prod
  syncPolicy:
    automated:
      enabled: true
      prune: true
      selfHeal: true
      allowEmpty: false
    retry:
      limit: 5
      backoff:
        duration: 10s
        factor: 2
        maxDuration: 3m
    syncOptions:
      - CreateNamespace=true
      - PruneLast=true
      - ServerSideApply=true
  revisionHistoryLimit: 20
```

## 11.3 Fleet ApplicationSet with Safe Modification Policy

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: payments-prod-fleet
  namespace: argocd
spec:
  goTemplate: true
  goTemplateOptions:
    - missingkey=error
  generators:
    - clusters:
        selector:
          matchLabels:
            environment: prod
            payments-enabled: "true"
  syncPolicy:
    applicationsSync: create-update
    preserveResourcesOnDeletion: true
  template:
    metadata:
      name: 'payments-{{.name}}'
      labels:
        service: payments
        environment: prod
        rollout-ring: '{{index .metadata.labels "rollout-ring"}}'
    spec:
      project: payments
      source:
        repoURL: ssh://git@git.example.com/payments/env-config.git
        targetRevision: 8c21f8a71d9d9b6f8c0a0a17f9de000000000000
        path: 'clusters/{{.name}}/payments'
      destination:
        server: '{{.server}}'
        namespace: payments-prod
      syncPolicy:
        automated:
          enabled: true
          prune: true
          selfHeal: true
        syncOptions:
          - CreateNamespace=true
          - PruneLast=true
```

## 11.4 Argo CD RBAC Baseline

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  scopes: '[groups, email]'
  policy.csv: |
    p, role:payments-viewer, applications, get, payments/*, allow
    p, role:payments-viewer, logs, get, payments/*, allow
    p, role:payments-operator, applications, get, payments/*, allow
    p, role:payments-operator, applications, sync, payments/*, allow
    p, role:payments-operator, applications, action/*, payments/*, allow
    p, role:platform-admin, *, *, *, allow
    g, payments-developers, role:payments-viewer
    g, payments-sre, role:payments-operator
    g, platform-sre, role:platform-admin
```

## 11.5 PreSync Database Migration Hook

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: payments-schema-migration
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: BeforeHookCreation,HookSucceeded
    argocd.argoproj.io/sync-wave: "-10"
spec:
  backoffLimit: 1
  activeDeadlineSeconds: 600
  template:
    metadata:
      labels:
        app: payments-schema-migration
    spec:
      serviceAccountName: payments-migrator
      restartPolicy: Never
      containers:
        - name: migrate
          image: registry.example.com/payments-migrate@sha256:REPLACE_WITH_DIGEST
          envFrom:
            - secretRef:
                name: payments-db-runtime
```

---

# 12. Command and Troubleshooting Cheat Sheet

## Authentication and Version

```bash
argocd version
argocd login argocd.example.com --sso --grpc-web
argocd account get-user-info
argocd account list
```

## Applications

```bash
argocd app list
argocd app get APP
argocd app get APP --refresh
argocd app get APP --hard-refresh
argocd app diff APP
argocd app manifests APP
argocd app resources APP
argocd app history APP
argocd app sync APP --revision GIT_SHA
argocd app wait APP --sync --health --operation --timeout 900
argocd app terminate-op APP
```

## Repositories and Certificates

```bash
argocd repo list
argocd repo get REPO_URL --refresh hard
argocd repocreds list
argocd cert list --cert-type https
argocd cert list --cert-type ssh
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=repository
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=repo-creds
```

## Clusters and Projects

```bash
argocd cluster list
argocd cluster get CLUSTER
argocd proj list
argocd proj get PROJECT
argocd proj windows list PROJECT
kubectl -n argocd get secret -l argocd.argoproj.io/secret-type=cluster
```

## Component Health and Logs

```bash
kubectl -n argocd get pod -o wide
kubectl -n argocd get events --sort-by=.lastTimestamp
kubectl -n argocd top pod
kubectl -n argocd logs deploy/argocd-server --since=30m
kubectl -n argocd logs deploy/argocd-repo-server --since=30m
kubectl -n argocd logs statefulset/argocd-application-controller --since=30m
kubectl -n argocd logs deploy/argocd-applicationset-controller --since=30m
kubectl -n argocd logs deploy/argocd-notifications-controller --since=30m
```

## Common Symptom Map

| Symptom | First component | First checks |
|---|---|---|
| UI unavailable | `argocd-server` | Route/Ingress, Service, TLS, pod readiness, SSO |
| Repository fetch/render failure | `argocd-repo-server` | credentials, CA/host key, DNS, disk, memory, tool/plugin |
| App state stale or sync failing | `argocd-application-controller` | target API, queue, shard, RBAC, live-resource error |
| Wrong generated Applications | ApplicationSet controller | generator output, labels/files, template, policy |
| Notifications missing | Notifications controller | trigger, subscription, service Secret, outbound network |
| All components show cache errors | Redis | pod health, auth, network, memory, latency |

---

# 13. Mock Interview Labs

## Lab 1 — Build a Restricted Team Platform

**Requirement:** A team may deploy only from one Git repository to namespaces matching `team-a-*` on two clusters. It may not create CRDs, admission webhooks, or ClusterRoleBindings.

**Expected solution:**

1. Create a dedicated AppProject.
2. Restrict `sourceRepos` and `destinations`.
3. Add cluster resource deny lists.
4. Add target-cluster RBAC and admission policy.
5. Map the team's SSO group to read/sync permissions only.
6. Test prohibited manifests and verify both Argo CD and API-server rejection.

## Lab 2 — Diagnose Permanent OutOfSync

**Scenario:** A Deployment is OutOfSync only on `/spec/replicas`; an HPA is active.

**Expected solution:**

1. Verify HPA ownership and managed fields.
2. Decide whether Git or HPA is authoritative for replicas.
3. Remove static replicas from the rendered manifest where practical, or add a narrowly scoped diff ignore.
4. Verify self-heal no longer fights the HPA.
5. Ensure other Deployment fields remain protected.

## Lab 3 — Recover from a Broken Helm Chart

**Scenario:** A chart version was overwritten and now renders different manifests for the same version.

**Expected solution:**

1. Freeze automated sync for affected Applications.
2. Retrieve the last known-good artifact from a trusted mirror or backup.
3. Publish a new immutable chart version; never overwrite again.
4. Update Git to the new version/digest.
5. Add registry immutability and signature/provenance policy.

## Lab 4 — Multi-Cluster Blast-Radius Control

**Requirement:** Deploy to 100 clusters in three rings with a maximum of five clusters in the first ring.

**Expected solution:**

1. Label cluster Secrets with a rollout ring.
2. Use ApplicationSet generation and, where approved, Progressive Sync RollingSync strategy.
3. Validate health and business metrics between rings.
4. Provide an emergency stop and Git revert path.
5. Keep a separate instance or Project boundary for critical regulated clusters.

## Lab 5 — Argo CD Disaster Recovery

**Scenario:** The management cluster is lost, but workload clusters continue running.

**Expected solution:**

1. Rebuild the management cluster and install the pinned Argo CD version.
2. Restore configuration, secrets, trusted CAs, SSO, Projects, Applications, and ApplicationSets.
3. Initially disable automated prune/self-heal or isolate target access.
4. Compare desired and live state.
5. Progressively resume reconciliation.
6. Rotate restored credentials and document RTO/RPO.

## Lab 6 — OpenShift GitOps Operator Drift

**Scenario:** An engineer edits the repo-server Deployment directly to increase memory, but the setting is later reverted.

**Expected solution:**

1. Explain Operator reconciliation.
2. Identify the owning `ArgoCD` custom resource.
3. Update supported resource fields in the CR.
4. Verify Operator status and generated Deployment.
5. Remove ad hoc direct-edit permissions and add Git-managed Operator configuration.

---

# 14. Official References

The following primary sources were used to validate architecture and current behavior. Always consult the documentation matching the exact installed release.

1. Argo CD documentation: <https://argo-cd.readthedocs.io/>
2. Architectural overview: <https://argo-cd.readthedocs.io/en/stable/operator-manual/architecture/>
3. High availability and scaling: <https://argo-cd.readthedocs.io/en/stable/operator-manual/high_availability/>
4. Declarative setup: <https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/>
5. AppProjects: <https://argo-cd.readthedocs.io/en/stable/user-guide/projects/>
6. Automated sync: <https://argo-cd.readthedocs.io/en/stable/user-guide/auto_sync/>
7. Sync phases and waves: <https://argo-cd.readthedocs.io/en/stable/user-guide/sync-waves/>
8. Sync options: <https://argo-cd.readthedocs.io/en/stable/user-guide/sync-options/>
9. ApplicationSet: <https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/>
10. ApplicationSet generators: <https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Generators/>
11. Progressive Syncs: <https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Progressive-Syncs/>
12. ApplicationSet specification: <https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/applicationset-specification/>
13. RBAC: <https://argo-cd.readthedocs.io/en/stable/operator-manual/rbac/>
14. Private repositories: <https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/>
15. Notifications: <https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/>
16. Upgrade overview: <https://argo-cd.readthedocs.io/en/stable/operator-manual/upgrading/overview/>
17. Argo CD GitHub releases: <https://github.com/argoproj/argo-cd/releases>
18. Red Hat OpenShift GitOps documentation: <https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/>

---

## Final L3 Interview Advice

For every scenario, show that you understand four independent states:

- **Source integrity:** Is the repository revision authentic, immutable, and renderable?
- **Controller integrity:** Are Argo CD components healthy, authorized, and keeping up with load?
- **Cluster convergence:** Does live state match desired state without hidden ownership conflicts?
- **Application outcome:** Is the service actually healthy for users and safe to promote?

A production-grade answer always mentions blast radius, least privilege, evidence collection, rollback, and how the accepted final state returns to Git.
