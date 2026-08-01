# HashiCorp Vault Corporate L3 — Interview Questions and Detailed Answers

> **Target audience:** Senior Linux, DevOps, DevSecOps, SRE, Platform Engineering, Kubernetes/OpenShift, IAM, PKI, and Cloud Security professionals with approximately 8–12+ years of experience  
> **Interview level:** Corporate L3 / Senior Engineer / Technical Lead / Vault Administrator / Platform Security Engineer  
> **Document version:** 1.0  
> **Vault baseline:** HashiCorp Vault v2.0.3, released June 17, 2026  
> **Important:** Vault APIs, plugins, licensing, and operational behavior evolve. Validate commands, API paths, feature availability, upgrade order, and Enterprise requirements against the exact version and edition installed in the target environment.

---

## Table of Contents

1. [Vault Fundamentals and Architecture](#1-vault-fundamentals-and-architecture)
2. [Initialization, Seal, Unseal, and Recovery](#2-initialization-seal-unseal-and-recovery)
3. [Storage, Integrated Raft, and High Availability](#3-storage-integrated-raft-and-high-availability)
4. [Authentication Methods and Identity](#4-authentication-methods-and-identity)
5. [Tokens, Leases, Renewals, and Revocation](#5-tokens-leases-renewals-and-revocation)
6. [Policies, ACLs, Sentinel, and Governance](#6-policies-acls-sentinel-and-governance)
7. [KV and General Secrets Management](#7-kv-and-general-secrets-management)
8. [Database, Cloud, and Dynamic Secrets](#8-database-cloud-and-dynamic-secrets)
9. [Transit, PKI, SSH, TOTP, and Specialized Engines](#9-transit-pki-ssh-totp-and-specialized-engines)
10. [Kubernetes and OpenShift Integration](#10-kubernetes-and-openshift-integration)
11. [Vault Agent, Injector, CSI, and Secrets Operator](#11-vault-agent-injector-csi-and-secrets-operator)
12. [Namespaces, Replication, and Multi-Cluster Design](#12-namespaces-replication-and-multi-cluster-design)
13. [Audit, Telemetry, Performance, and Capacity](#13-audit-telemetry-performance-and-capacity)
14. [Security Hardening and Enterprise Operations](#14-security-hardening-and-enterprise-operations)
15. [Backup, Restore, Disaster Recovery, and Upgrades](#15-backup-restore-disaster-recovery-and-upgrades)
16. [Corporate L3 Troubleshooting Scenarios](#16-corporate-l3-troubleshooting-scenarios)
17. [Rapid-Fire Commands and Diagnostic Checklists](#17-rapid-fire-commands-and-diagnostic-checklists)
18. [Senior Interview Response Framework](#18-senior-interview-response-framework)
19. [Official References](#19-official-references)

---

# 1. Vault Fundamentals and Architecture

## Q1. What is HashiCorp Vault?

### Answer

HashiCorp Vault is a secrets-management, encryption, identity-brokering, and privileged-access platform.

Its core capabilities include:

- Centralized storage of static secrets.
- Dynamic credential generation.
- Encryption as a service.
- PKI certificate issuance.
- Identity-based access control.
- Leasing, renewal, and revocation.
- Audit logging.
- Kubernetes and cloud authentication.
- High availability.
- Integrated Raft storage.
- Enterprise namespaces.
- Performance and disaster-recovery replication.
- Hardware Security Module and managed-key integrations, depending on edition and configuration.

A senior answer must explain that Vault is not merely an encrypted key/value database. It is an identity-aware broker that issues short-lived credentials and applies policy to every API path.

---

## Q2. What security problem does Vault solve?

### Answer

Vault reduces risks associated with:

- Hard-coded credentials.
- Long-lived shared passwords.
- Secrets stored in Git.
- Credentials copied into CI variables.
- Manual certificate issuance.
- Untracked privilege escalation.
- Lack of revocation.
- Inconsistent access policy.
- Secrets spread across many platforms.

Vault provides a controlled trust boundary where an authenticated identity receives only authorized secrets or cryptographic operations for a limited lifetime.

Vault does not automatically secure an application that writes the retrieved secret to logs, environment dumps, crash files, or an unprotected filesystem.

---

## Q3. Explain Vault's high-level request flow.

### Answer

A typical request follows this sequence:

1. A client connects to a Vault API endpoint over TLS.
2. The client authenticates through an auth method.
3. Vault maps the authenticated principal to an identity entity and policy set.
4. Vault evaluates ACL and governance policies against the API path and operation.
5. The router sends the request to the appropriate secrets engine, auth method, or system backend.
6. The backend returns data, creates a lease, performs cryptography, or modifies storage.
7. Vault records the operation through enabled audit devices.
8. The client receives a response, often including a token, lease ID, duration, or renewable flag.

The active node handles writes. Standby behavior depends on HA mode, performance standby capability, and replication architecture.

---

## Q4. What are the main internal components of Vault?

### Answer

Important components include:

- **HTTP API listener:** Receives client requests.
- **Core:** Coordinates routing, policy evaluation, leases, identity, and system functions.
- **Barrier:** Encrypts data before it is written to durable storage.
- **Storage backend:** Persists encrypted Vault data.
- **Seal mechanism:** Protects the key material needed to decrypt the barrier key.
- **Auth methods:** Authenticate users and workloads.
- **Secrets engines:** Store or generate secrets and perform cryptographic operations.
- **Token store:** Manages Vault tokens.
- **Expiration manager:** Tracks leases and revocation.
- **Identity system:** Maps aliases from multiple auth methods to entities and groups.
- **Audit subsystem:** Records requests and responses through configured audit devices.
- **HA subsystem:** Elects an active node and manages standbys.
- **Replication subsystem:** Replicates data between Enterprise clusters.

---

## Q5. What is the security barrier?

### Answer

The security barrier is Vault's encryption layer between the logical Vault data and the storage backend.

Vault encrypts sensitive stored data before writing it to storage. The storage backend therefore receives encrypted values and metadata rather than plaintext secret content.

The barrier key is itself protected by the configured seal mechanism.

Important implications:

- Storage administrators should not be able to read Vault secrets directly.
- Storage compromise is still serious because attackers may destroy, roll back, or copy encrypted data.
- The storage backend must still be secured, monitored, backed up, and protected from unauthorized modification.
- Vault must hold sensitive key material in memory while unsealed.

---

## Q6. What is a secrets engine?

### Answer

A secrets engine is a mounted backend that provides secret-management or cryptographic functionality at a path.

Examples:

```text
secret/
database/
pki/
transit/
ssh/
aws/
azure/
gcp/
totp/
```

Enable and inspect engines:

```bash
vault secrets list
vault secrets enable -path=apps kv-v2
vault secrets tune -description="Application secrets" apps/
```

Multiple instances of the same engine type can be mounted at different paths with independent configuration and policy.

---

## Q7. What is an authentication method?

### Answer

An auth method verifies a user, workload, machine, or external identity and returns a Vault token.

Examples:

- Token
- AppRole
- Kubernetes
- JWT/OIDC
- LDAP
- Userpass
- AWS
- Azure
- GCP
- TLS certificates
- GitHub, where supported
- Radius, depending on version and requirements

Auth methods are mounted at paths such as:

```text
auth/kubernetes/
auth/approle/
auth/oidc/
```

Multiple mounts of the same auth type can serve different trust domains.

---

## Q8. Why is everything in Vault path-based?

### Answer

Vault APIs, mounts, secrets, auth methods, and ACL policies are organized around paths.

Examples:

```text
secret/data/payments/config
database/creds/payments-readonly
pki/issue/internal-services
auth/kubernetes/login
sys/health
```

Policy maps capabilities to path patterns.

A path-based design provides a consistent authorization model across different backend types, but policy authors must understand exact API paths, especially KV v2's separate `data/` and `metadata/` routes.

---

## Q9. What is the difference between Vault Community and Vault Enterprise?

### Answer

Vault Community provides core secrets-management capabilities, including auth methods, secrets engines, policies, audit devices, HA with supported storage, integrated Raft storage, and snapshots.

Vault Enterprise adds capabilities that may include:

- Namespaces.
- Performance replication.
- Disaster-recovery replication.
- Performance standby features.
- Sentinel policy governance.
- HSM and seal-wrap features.
- Control groups.
- MFA-related enterprise workflows.
- Advanced data protection and governance features.
- Secrets sync and additional enterprise plugins.
- Advanced reporting or administrative features, depending on license tier and version.

Always verify current edition and license entitlements before proposing a design.

---

## Q10. How is Vault different from Kubernetes Secrets?

### Answer

Kubernetes Secrets are Kubernetes API objects. Their values are base64-encoded in manifests and stored according to cluster encryption-at-rest configuration.

Vault provides:

- Dynamic credentials.
- Independent identity and policy.
- Leasing and revocation.
- PKI.
- encryption as a service.
- audit logging.
- multi-platform integration.
- secret rotation workflows.

Kubernetes Secrets remain useful for applications that require native Secret objects. The architecture must account for the extra copy of secret data placed into etcd, controller caches, backups, and pod-accessible volumes.

---

## Q11. How is Vault different from cloud-native secret managers?

### Answer

Cloud secret managers are tightly integrated with their cloud IAM, availability model, and managed operations.

Vault can provide:

- Multi-cloud consistency.
- On-premises integration.
- Dynamic database credentials.
- Transit encryption.
- PKI.
- custom plugins.
- unified identity and policy.
- self-managed or managed deployment options.

The trade-off is operational responsibility. Self-managed Vault requires expertise in HA, storage, TLS, upgrades, backup, seal design, audit, and incident response.

---

## Q12. What are the common Vault deployment models?

### Answer

- Self-managed Vault on virtual machines.
- Self-managed Vault on bare metal.
- Vault on Kubernetes or OpenShift.
- HCP Vault Dedicated or another supported managed model.
- Regional Vault clusters with Enterprise replication.
- Central Vault with workload clusters consuming secrets remotely.
- Vault Agent or operator-based distribution to applications.

Production hardening guidance generally favors strong isolation. Container deployment may be operationally appropriate, but the threat model and platform controls must be documented.

---

# 2. Initialization, Seal, Unseal, and Recovery

## Q13. What happens during `vault operator init`?

### Answer

Initialization:

- Creates Vault's initial root key hierarchy.
- Configures the initial seal state.
- Produces Shamir unseal-key shares or recovery-key shares, depending on seal type.
- Produces an initial root token.
- Prepares the storage backend for use.

Example:

```bash
vault operator init \
  -key-shares=5 \
  -key-threshold=3
```

Initialization is performed once per new Vault cluster. Running initialization against the wrong empty storage location can create a different cluster and cause operational confusion.

---

## Q14. What is Shamir's Secret Sharing in Vault?

### Answer

With Shamir seal, Vault splits the unseal key into multiple shares. A configured threshold of shares is needed to reconstruct the key material required to unseal Vault.

Example:

- Total shares: 5
- Threshold: 3

No single share is sufficient.

Operational controls:

- Assign shares to independent trusted custodians.
- Encrypt shares with individual PGP keys.
- Never store all shares together.
- Test unseal and rekey procedures.
- Define loss, departure, and compromise procedures.
- Protect the initial root token separately.

---

## Q15. What does sealed mean?

### Answer

A sealed Vault can access storage but cannot decrypt the barrier-protected data needed for normal operation.

While sealed:

- Normal secret and auth operations are unavailable.
- Health endpoints report sealed state.
- Initialization or unseal operations may still be available as appropriate.
- The process can be running and listening even though Vault is not operational.

Check:

```bash
vault status
curl -k https://vault.example.com:8200/v1/sys/health
```

---

## Q16. What is manual unseal?

### Answer

Manual unseal requires submitting enough Shamir key shares to reach the threshold.

```bash
vault operator unseal
```

Each Vault node must be unsealed individually when using Shamir seal.

Limitations:

- Human coordination after restart.
- delayed recovery.
- difficult autoscaling.
- operational risk during large outages.
- key-share handling burden.

Manual unseal may suit small or highly controlled environments but often does not meet enterprise recovery objectives.

---

## Q17. What is auto-unseal?

### Answer

Auto-unseal uses an external cryptographic service or HSM to unwrap Vault's protected root key material when the process starts.

Supported mechanisms can include cloud KMS systems, HSMs, and another Vault transit engine, depending on edition and configuration.

Benefits:

- Faster restart.
- reduced manual intervention.
- easier orchestration.

Critical risk:

- Vault becomes dependent on the external seal mechanism.
- Recovery keys do not replace the KMS/HSM key used to decrypt the root key.
- Permanent loss of the auto-unseal key can make the cluster unrecoverable, including from snapshots.

---

## Q18. What is the difference between unseal keys and recovery keys?

### Answer

With Shamir seal:

- Operators receive unseal-key shares.
- The threshold can reconstruct the key required to unseal Vault.

With auto-unseal:

- Operators normally receive recovery-key shares.
- Recovery keys authorize sensitive recovery operations such as root generation or rekey.
- Recovery keys do not decrypt the barrier root key.
- They cannot unseal Vault if the auto-unseal provider and key are unavailable.

This is one of the most important Vault interview distinctions.

---

## Q19. What is seal wrapping?

### Answer

Seal wrapping is an Enterprise capability that applies an additional encryption layer using the configured seal mechanism to selected sensitive storage entries.

Use cases include protection of:

- root key material.
- replication keys.
- recovery data.
- other highly sensitive values.

Seal wrap increases cryptographic dependence on the HSM/KMS and can affect latency and operational procedures.

---

## Q20. What is rekeying?

### Answer

Rekeying changes the shares, threshold, or custodians without changing all underlying Vault data.

For Shamir unseal keys:

```bash
vault operator rekey
```

For recovery keys:

```bash
vault operator rekey -target=recovery
```

Use rekey when:

- A custodian leaves.
- A share may be compromised.
- Threshold design changes.
- PGP recipients change.
- Key-custody policy is rotated.

Rekeying is different from rotating the underlying encryption key hierarchy.

---

## Q21. How do you generate a new root token?

### Answer

Vault uses a quorum-based generate-root procedure.

Typical workflow:

```bash
vault operator generate-root -init
vault operator generate-root
vault operator generate-root -decode=<encoded-token> -otp=<otp>
```

The exact process depends on seal type and version.

Use only under controlled break-glass procedures:

- Multiple custodians.
- incident/change record.
- approved purpose.
- short lifetime of use.
- audit review.
- revoke the root token immediately after the task.

---

## Q22. Why should the initial root token be revoked?

### Answer

The initial root token has unrestricted authority and typically no normal expiration.

After initial configuration:

- Configure auth methods.
- Create administrative policies.
- Test operator access.
- enable audit.
- revoke the initial root token.

```bash
vault token revoke <root-token>
```

Future root access should use the quorum-based generate-root process.

---

## Q23. What is seal migration?

### Answer

Seal migration changes the cluster from:

- Shamir to auto-unseal.
- auto-unseal to Shamir.
- one auto-unseal mechanism to another.

It requires a carefully controlled rolling process and brief cluster downtime during the final migration stage.

Safety requirements:

- Current validated backup.
- exact old and new seal configuration.
- access to old key material.
- quorum preservation.
- `-migrate` during unseal steps.
- log monitoring for seal rewrap completion.
- rollback and vendor support plan.

---

## Q24. What is recovery mode?

### Answer

Recovery mode starts Vault with direct, restricted storage access for severe cases in which the normal Vault process cannot start correctly.

```bash
vault server -recovery -config=/etc/vault.d
```

Recovery mode is not a general troubleshooting shortcut. It can bypass normal workflows and should normally be used with expert or vendor support after preserving storage backups.

---

# 3. Storage, Integrated Raft, and High Availability

## Q25. What information is stored in the Vault storage backend?

### Answer

Vault storage contains encrypted representations of:

- Secrets.
- auth configuration.
- policies.
- token and lease state.
- identity data.
- mounts.
- PKI key and issuer data.
- transit key material.
- Raft metadata when using integrated storage.
- replication metadata.
- internal system state.

Audit logs are not normally part of the Vault storage backend; they are written through separately configured audit devices.

---

## Q26. What is integrated storage?

### Answer

Integrated storage is Vault's built-in Raft-based storage backend.

It provides:

- Durable Vault storage.
- HA coordination.
- consensus replication between Vault nodes.
- snapshot backup/restore.
- Enterprise replication compatibility.
- removal of a separate Consul dependency for storage.

Data is stored locally on each Raft voter and replicated through the consensus protocol.

---

## Q27. Why is an odd number of Raft voters recommended?

### Answer

Raft requires a majority quorum.

Examples:

- 3 voters tolerate 1 voter failure.
- 5 voters tolerate 2 voter failures.
- 4 voters still tolerate only 1 voter failure.

An odd voter count avoids paying for an extra voter without gaining another failure tolerance level.

Operational placement matters more than raw count. Distribute voters across independent nodes and failure domains with reliable low-latency communication.

---

## Q28. What are the default Vault ports?

### Answer

Common defaults:

- `8200/tcp`: Client API traffic.
- `8201/tcp`: Cluster-to-cluster communication.

The cluster port uses mutual TLS managed by Vault for internal communication after cluster join.

Firewall and NetworkPolicy design must allow:

- Clients/load balancers to API listeners.
- Vault nodes to communicate over the cluster address.
- Vault nodes to storage, KMS, identity, audit, and external secret systems as required.

---

## Q29. What is the difference between `api_addr` and `cluster_addr`?

### Answer

`api_addr` is the advertised address clients or other nodes use for API redirection and connectivity.

`cluster_addr` is the address used for internal cluster communication.

Example:

```hcl
api_addr     = "https://vault-1.example.com:8200"
cluster_addr = "https://vault-1.example.com:8201"
```

Incorrect addresses can cause:

- Redirect loops.
- join failures.
- standby forwarding failures.
- TLS name mismatches.
- replication communication issues.

---

## Q30. How does Vault HA work?

### Answer

Vault HA uses:

- One active node.
- Multiple standby nodes.
- A storage backend that supports HA semantics.
- Leader election and lock coordination.
- request forwarding or redirection from standby nodes.

Only the active node performs normal storage writes.

HA protects against individual node failure. It is not by itself:

- A backup.
- geographic disaster recovery.
- protection from operator deletion.
- protection from corrupted or malicious replicated state.

---

## Q31. What is a performance standby?

### Answer

Performance standby is an Enterprise feature that allows eligible standby nodes to service many read-only and cryptographic requests instead of forwarding every request to the active node.

Writes and some consistency-sensitive requests still require the active node.

Benefits:

- Increased read throughput.
- reduced load on the active node.
- improved horizontal scalability.

Design must account for eventual consistency and client read-after-write expectations.

---

## Q32. How do you inspect Raft peer status?

### Answer

```bash
vault operator raft list-peers
vault operator raft autopilot state
```

Check:

- Node IDs.
- addresses.
- leader.
- voter status.
- Raft protocol state.
- healthy/optimistic state.
- last contact.
- failure tolerance.
- upgrade version compatibility.

Also inspect logs and storage latency.

---

## Q33. What is Raft Autopilot?

### Answer

Autopilot monitors and helps manage the health of the integrated-storage Raft cluster.

Functions can include:

- Health evaluation.
- dead-server cleanup.
- upgrade/version assessment.
- redundant non-voter management in supported Enterprise configurations.
- stability checks before membership changes.

Autopilot does not eliminate the need to understand quorum and failure domains.

---

## Q34. How do you join a node to a Raft cluster?

### Answer

Example:

```bash
vault operator raft join https://vault-active.example.com:8200
```

The new node must:

- Use the correct storage configuration.
- use a compatible seal.
- reach the active API address.
- trust TLS.
- have a unique node ID.
- use persistent empty storage appropriate for joining.
- be unsealed or auto-unseal correctly.

Once joined, a node cannot be casually rejoined to a different cluster without clearing and rebuilding its local state through an approved process.

---

## Q35. What causes loss of Raft quorum?

### Answer

- Too many voter nodes unavailable.
- network partition.
- firewall or NetworkPolicy errors.
- storage stalls.
- node identity/address mistakes.
- simultaneous maintenance.
- failed zone.
- corrupted or lost disks.
- certificate or seal problems preventing nodes from starting.

Without quorum, the cluster cannot safely commit writes and may not elect an active leader.

Do not attempt random peer removal during a quorum incident.

---

## Q36. What is a Raft snapshot?

### Answer

A Raft snapshot is a point-in-time backup of integrated-storage data.

Create:

```bash
vault operator raft snapshot save vault-$(date +%F).snap
```

Inspect:

```bash
vault operator raft snapshot inspect vault-2026-08-01.snap
```

Restore:

```bash
vault operator raft snapshot restore vault-2026-08-01.snap
```

Use `-force` only when seal-key consistency and recovery implications are fully understood.

Snapshots must be encrypted, access-controlled, copied off-cluster, and regularly restored in a test environment.

---

## Q37. Is a Raft snapshot enough for disaster recovery?

### Answer

A snapshot is an essential backup, but not a complete DR strategy.

A complete design also requires:

- Restore infrastructure.
- correct Vault binaries and plugins.
- seal/KMS/HSM availability.
- TLS certificates and DNS.
- configuration files.
- load balancers.
- auth-provider connectivity.
- audit destinations.
- tested procedures.
- recovery-time and recovery-point objectives.
- Enterprise DR replication where required.

A snapshot cannot be used if the required auto-unseal key has been permanently destroyed.

---

## Q38. How do you remove a failed Raft peer safely?

### Answer

1. Confirm the peer is permanently gone.
2. confirm the cluster still has quorum.
3. identify exact node ID.
4. take a current snapshot.
5. remove through supported API/CLI:

```bash
vault operator raft remove-peer <node-id>
```

6. verify peer state and failure tolerance.
7. replace capacity if required.
8. investigate root cause.

Removing the wrong peer can destroy quorum.

---

# 4. Authentication Methods and Identity

## Q39. How does token authentication work?

### Answer

Every normal authenticated Vault request carries a Vault token.

Common methods:

```bash
export VAULT_TOKEN=<token>
vault login <token>
```

The token represents:

- Attached policies.
- identity association.
- TTL.
- renewability.
- parent relationship.
- usage limits.
- metadata.
- namespace context.

Applications should normally authenticate through an appropriate auth method rather than receiving manually created long-lived tokens.

---

## Q40. What is AppRole?

### Answer

AppRole is a machine-oriented auth method using:

- Role ID.
- Secret ID or another controlled login constraint.
- role configuration.
- policies and token settings.

Enable:

```bash
vault auth enable approle
```

Login path:

```text
auth/approle/login
```

AppRole is suitable for systems without a stronger native identity, but secure Secret ID delivery remains a bootstrap problem.

---

## Q41. How do you secure AppRole?

### Answer

- Protect Role ID as an identifier, not necessarily as the sole secret.
- Deliver Secret ID through a trusted channel.
- Use response wrapping.
- set Secret ID TTL.
- use low or one-time usage limits.
- restrict CIDRs where appropriate.
- use short token TTL.
- avoid broad policies.
- rotate Role IDs if exposed.
- audit accessor and metadata.
- never commit Secret IDs to Git or bake them into images.

---

## Q42. What is response wrapping?

### Answer

Response wrapping places a sensitive response inside a short-lived single-use wrapping token.

Example:

```bash
vault write -wrap-ttl=5m \
  auth/approle/role/payments/secret-id
```

The receiving workload unwraps:

```bash
vault unwrap <wrapping-token>
```

Benefits:

- The original secret is not exposed to the intermediary.
- Single use.
- short TTL.
- cubbyhole-based delivery.
- tamper or interception can be detected because a second unwrap fails.

---

## Q43. How does Kubernetes authentication work?

### Answer

1. A pod presents a Kubernetes ServiceAccount JWT to Vault.
2. Vault validates the token through configured Kubernetes API/token-review mechanisms or supported JWT validation behavior.
3. Vault checks the ServiceAccount name, namespace, audience, and role constraints.
4. Vault returns a Vault token with mapped policies.

Configuration example:

```bash
vault auth enable kubernetes

vault write auth/kubernetes/config \
  kubernetes_host="https://kubernetes.default.svc:443" \
  kubernetes_ca_cert=@ca.crt
```

Role example:

```bash
vault write auth/kubernetes/role/payments \
  bound_service_account_names=payments \
  bound_service_account_namespaces=prod-payments \
  policies=payments-read \
  audience=vault \
  ttl=30m
```

---

## Q44. What are common Kubernetes auth mistakes?

### Answer

- Binding the default ServiceAccount.
- wildcard namespaces.
- wildcard ServiceAccount names.
- wrong audience.
- expired projected token.
- token reviewer RBAC failure.
- CA mismatch.
- wrong Kubernetes API address.
- cluster issuer mismatch.
- reusing one auth mount for unrelated clusters without clear trust separation.
- overly long Vault token TTL.
- not validating namespace boundaries.
- not rotating legacy ServiceAccount tokens.

---

## Q45. How does JWT/OIDC authentication work?

### Answer

Vault validates a JWT against:

- Trusted issuer.
- signing keys or discovery endpoint.
- audience.
- bound claims.
- claim mappings.
- role type.
- allowed redirect URIs for OIDC interactive login.

JWT roles are typically used for machine login. OIDC roles support interactive browser login and user identity.

A valid signature alone is insufficient. Bound issuer, audience, subject, and relevant claims must be constrained.

---

## Q46. How does LDAP authentication work?

### Answer

Vault authenticates user credentials against LDAP and maps LDAP groups or users to Vault policies.

Key design points:

- LDAPS or StartTLS.
- trusted CA.
- service-account bind credential protection.
- user DN and group filter correctness.
- nested group behavior.
- group alias mapping.
- failover servers.
- connection timeout.
- lockout policy.
- audit privacy.
- migration to SSO/OIDC where appropriate.

LDAP credentials pass through Vault, so Vault becomes part of the credential-handling path.

---

## Q47. What is an identity entity?

### Answer

An entity represents a logical person or workload identity inside Vault.

An entity can have multiple aliases, such as:

- LDAP username.
- OIDC subject.
- Kubernetes ServiceAccount.
- AppRole identity.

Policies can be attached through:

- Auth role/token.
- entity.
- groups.

Entity merging enables one logical identity across multiple auth methods, but incorrect alias mapping can create privilege escalation.

---

## Q48. What is an entity alias?

### Answer

An alias connects an external auth-method identity to a Vault entity.

It includes:

- Alias name.
- auth mount accessor.
- canonical entity ID.
- metadata.

The mount accessor is important because the same username on two separate auth mounts does not automatically represent the same identity.

---

## Q49. What are internal and external identity groups?

### Answer

Internal groups are explicitly managed within Vault.

External groups map group information from external identity providers through group aliases.

Groups can provide policies to member entities.

Use external groups for centralized identity governance, but validate:

- Exact group claim or LDAP group.
- auth mount accessor.
- alias mapping.
- nested membership.
- stale group removal.
- policy attachment review.

---

## Q50. How do you troubleshoot login failures?

### Answer

Check:

```bash
vault auth list -detailed
vault read auth/<mount>/config
vault read auth/<mount>/role/<role>
vault audit list
vault status
```

Investigate:

- Exact auth path.
- role name.
- issuer and audience.
- bound claims.
- ServiceAccount namespace.
- LDAP bind/group filter.
- AppRole Secret ID TTL and uses.
- client clock.
- TLS.
- namespace header.
- audit logs.
- policy versus authentication.

Separate **authentication failure** from **authenticated but permission denied**.

---

# 5. Tokens, Leases, Renewals, and Revocation

## Q51. What types of Vault tokens exist?

### Answer

Important distinctions include:

- Service tokens.
- Batch tokens.
- Root tokens.
- Orphan tokens.
- Periodic tokens.
- Renewable and non-renewable tokens.
- Limited-use tokens.
- Tokens with explicit maximum TTL.

Service tokens are persisted and support the full token lifecycle.

Batch tokens are lightweight encrypted tokens useful for high-scale workflows, with behavior and feature limitations compared with service tokens.

---

## Q52. What is a token accessor?

### Answer

An accessor is a non-secret identifier for a token.

It can be used for:

- Lookup.
- revocation.
- audit correlation.
- token management without revealing the token value.

```bash
vault token lookup -accessor <accessor>
vault token revoke -accessor <accessor>
```

Accessors are less sensitive than tokens but should still be protected because they can enable administrative actions when combined with sufficient policy.

---

## Q53. What is token TTL?

### Answer

TTL is the remaining lifetime of a token or leased secret.

Token lifetime is constrained by:

- Requested TTL.
- auth-role settings.
- mount tuning.
- system default/max TTL.
- explicit max TTL.
- parent token lifetime.
- periodic behavior.
- renewal policy.

Inspect:

```bash
vault token lookup
vault read sys/auth
vault auth list -detailed
```

---

## Q54. What is a periodic token?

### Answer

A periodic token can be renewed repeatedly for a configured period as long as policy permits renewal and the role still allows it.

It does not use a normal fixed maximum lifetime in the same way as ordinary renewable tokens.

Use cases:

- Long-running services.
- Vault Agent-managed authentication.

Risks:

- If the application is compromised and can renew indefinitely, access may persist.
- Revocation and monitoring remain essential.
- Period should be short enough for operational control.

---

## Q55. What is an orphan token?

### Answer

An orphan token has no parent token.

Normal child tokens are revoked when their parent is revoked. Orphan tokens are independent.

Useful for:

- Long-running service identity that should not depend on an operator's session.
- controlled token-broker patterns.

Create through appropriate auth-role settings or authorized token creation paths. Avoid creating orphans casually because they weaken parent-based revocation trees.

---

## Q56. What is a lease?

### Answer

A lease is Vault's record of a time-limited secret or credential.

A leased response may contain:

```json
{
  "lease_id": "database/creds/payments/...",
  "lease_duration": 3600,
  "renewable": true
}
```

Vault can:

- Renew the lease.
- revoke the lease.
- automatically revoke after expiration.
- revoke all leases under a prefix.

Dynamic secret engines use leases to control credential lifecycle.

---

## Q57. How do renewal and revocation work?

### Answer

Renew:

```bash
vault lease renew <lease-id>
vault token renew
```

Revoke:

```bash
vault lease revoke <lease-id>
vault lease revoke -prefix database/creds/payments/
vault token revoke <token>
```

Renewal is not guaranteed. The backend, policy, role, maximum TTL, and parent token may prevent renewal.

Applications must handle renewal failure before the secret expires.

---

## Q58. What is revoke-prefix?

### Answer

Revoke-prefix revokes all leases below a path.

```bash
vault lease revoke -prefix database/creds/payments/
```

It is powerful and can cause a large production outage by invalidating every credential issued under the prefix.

Use:

- Impact assessment.
- maintenance window.
- application owner coordination.
- database capacity monitoring.
- rollback/reissue plan.

---

## Q59. What is `revoke-force`?

### Answer

Force revocation removes leases from Vault's tracking even when the backend revocation operation fails.

It may leave valid credentials active in the target system.

Use only when:

- The backend cannot be repaired in time.
- Manual cleanup is planned.
- Security and service owners approve.
- Orphaned external credentials are tracked.

Normal revocation is preferred.

---

## Q60. How should an application handle dynamic credentials?

### Answer

The application should:

1. Authenticate through a workload identity.
2. Request or receive a dynamic credential.
3. record expiration.
4. renew or fetch replacement before expiry.
5. establish new connections.
6. gracefully drain old connections.
7. avoid logging the secret.
8. respond to revocation.
9. retry with backoff.
10. fail safely if Vault is unavailable.

Database connection pools are a common source of failures because old connections may outlive revoked credentials or new connections may require rotated credentials.

---

# 6. Policies, ACLs, Sentinel, and Governance

## Q61. Explain Vault ACL policies.

### Answer

Vault ACL policies grant capabilities on API paths.

Example:

```hcl
path "secret/data/payments/*" {
  capabilities = ["read"]
}

path "secret/metadata/payments/*" {
  capabilities = ["list"]
}
```

Vault is deny-by-default. If no applicable policy grants a capability, access is denied.

Policies are additive except for explicit deny, which takes precedence.

---

## Q62. What are Vault policy capabilities?

### Answer

Common capabilities:

- `create`
- `read`
- `update`
- `patch`
- `delete`
- `list`
- `sudo`
- `deny`

The HTTP verb does not map one-to-one to the capability name. Check the API documentation for the path.

`sudo` grants access to root-protected endpoints and is highly sensitive.

---

## Q63. How do wildcard paths work?

### Answer

Vault policy path matching supports wildcard behavior such as:

- `*` as a glob suffix.
- `+` for a single path segment in supported policy syntax.

Example:

```hcl
path "secret/data/+/config" {
  capabilities = ["read"]
}
```

Test policy patterns because overly broad globs can expose unrelated tenants or administrative paths.

---

## Q64. Why is KV v2 policy authoring a common problem?

### Answer

KV v2 uses API subpaths such as:

```text
secret/data/app/config
secret/metadata/app/config
secret/delete/app/config
secret/undelete/app/config
secret/destroy/app/config
```

A UI/CLI logical path may appear as:

```text
secret/app/config
```

Policy authors must grant the correct `data/` and `metadata/` paths. Granting only `secret/app/*` often causes permission denied.

---

## Q65. How do you validate effective token permissions?

### Answer

```bash
vault token capabilities secret/data/payments/config
vault token capabilities <token> secret/data/payments/config
```

Also:

```bash
vault token lookup
vault read sys/policies/acl/<policy>
```

The final capability set may come from:

- token policies.
- identity entity policies.
- group policies.
- namespace context.
- Sentinel/governance controls.
- explicit deny.

---

## Q66. What is a templated ACL policy?

### Answer

Templated policies use identity metadata to create per-identity paths.

Conceptual example:

```hcl
path "secret/data/users/{{identity.entity.name}}/*" {
  capabilities = ["create", "read", "update", "delete"]
}
```

Use cases:

- User home secrets.
- team-based paths.
- application-specific isolation.

Template input must come from trusted identity metadata and should be tested for naming collisions and missing values.

---

## Q67. What is Sentinel in Vault?

### Answer

Sentinel is an Enterprise policy-as-code framework that can apply governance logic beyond standard ACL grants.

Policy levels can include:

- Advisory.
- soft mandatory.
- hard mandatory.

Use cases:

- Restricting token TTL.
- requiring metadata.
- limiting operations by time or environment.
- enforcing change rules.
- governance over administrative APIs.

ACL policy grants path capability; Sentinel can add contextual governance and may deny an otherwise authorized request.

---

## Q68. What is a control group?

### Answer

A control group is an Enterprise workflow that requires additional authorization before a sensitive request is completed.

Example:

- Operator requests production root credential.
- Vault returns a wrapping token.
- designated approvers authorize the request.
- requester unwraps the approved response.

Use for high-risk operations requiring separation of duties.

---

## Q69. How do you review policies for least privilege?

### Answer

Check:

- Wildcards.
- `sudo`.
- `sys/*`.
- token creation.
- identity administration.
- mount management.
- policy management.
- database root configuration.
- PKI signing.
- transit key deletion/export.
- KV metadata/destroy access.
- namespace administration.
- response wrapping.
- list capabilities exposing secret names.

Test with a token assigned only the intended policy.

---

## Q70. How do you manage policies as code?

### Answer

- Store HCL policies in Git.
- require peer review.
- run formatting and validation.
- test capabilities.
- render environment-specific values safely.
- deploy through CI/GitOps/Terraform.
- detect out-of-band changes.
- retain version history.
- link policies to owners and applications.
- include rollback.
- separate policy author and approver for critical access.

Do not store secret values in the same repository merely because policies are stored there.

---

# 7. KV and General Secrets Management

## Q71. What is the difference between KV v1 and KV v2?

### Answer

### KV v1

- Stores the latest value.
- Simple path model.
- No native version history.

### KV v2

- Versioned secrets.
- soft delete.
- undelete.
- permanent version destruction.
- check-and-set.
- metadata.
- configurable version retention.

KV v2 is generally preferred for static secrets when versioning is required.

---

## Q72. How do you enable KV v2?

### Answer

```bash
vault secrets enable -path=secret kv-v2
```

Or:

```bash
vault secrets enable -path=secret -version=2 kv
```

Verify:

```bash
vault secrets list -detailed
```

Do not re-enable a mount path containing existing data. Mount migration requires a documented procedure.

---

## Q73. How do you write and read KV v2 secrets?

### Answer

CLI:

```bash
vault kv put secret/payments/config \
  username=payments-app \
  password='example'

vault kv get secret/payments/config
vault kv get -version=2 secret/payments/config
```

API:

```text
POST /v1/secret/data/payments/config
GET  /v1/secret/data/payments/config
```

The CLI hides some API-path differences.

---

## Q74. What is check-and-set?

### Answer

Check-and-set prevents accidental overwrites.

Examples:

- CAS `0`: Write only if the key does not exist.
- CAS `n`: Write only if the current version is `n`.

```bash
vault kv put -cas=3 secret/payments/config password='new'
```

CAS is useful for CI and concurrent automation.

---

## Q75. What is the difference between delete, undelete, and destroy?

### Answer

In KV v2:

- **Delete:** Soft-deletes selected versions.
- **Undelete:** Restores soft-deleted versions.
- **Destroy:** Permanently removes version data.
- **Metadata delete:** Removes all versions and metadata for the key.

Examples:

```bash
vault kv delete secret/payments/config
vault kv undelete -versions=2 secret/payments/config
vault kv destroy -versions=1 secret/payments/config
vault kv metadata delete secret/payments/config
```

Destroy operations require strong policy and change control.

---

## Q76. Why should Vault not be used as a large binary store?

### Answer

Vault is optimized for secrets and cryptographic workflows, not large files or application datasets.

Large values can cause:

- Storage growth.
- Raft replication overhead.
- snapshot growth.
- API latency.
- memory pressure.
- audit log volume.
- backup and restore delay.

Store large encrypted objects in a suitable object store and use Vault to protect the encryption key or credentials.

---

## Q77. How do you rotate a static KV secret?

### Answer

A safe rotation process:

1. Identify consumers.
2. create new credential in target system.
3. write new value to Vault.
4. signal or reload applications.
5. verify consumers use the new value.
6. revoke old credential.
7. monitor failures.
8. document completion.

KV versioning provides rollback of stored values, but the external credential may already have been revoked.

---

# 8. Database, Cloud, and Dynamic Secrets

## Q78. How does the database secrets engine work?

### Answer

Vault stores a privileged database connection configuration and creates credentials according to roles.

Flow:

1. Application authenticates to Vault.
2. Application requests `database/creds/<role>`.
3. Vault connects to the database.
4. Vault executes creation statements.
5. Vault returns username, password, and lease.
6. At expiry or revocation, Vault runs revocation statements.

---

## Q79. What is a database role?

### Answer

A database role defines:

- Database connection name.
- credential type.
- creation statements.
- revocation statements.
- rollback statements.
- default TTL.
- maximum TTL.
- username templates where supported.

Example:

```bash
vault write database/roles/payments-readonly \
  db_name=payments-postgres \
  creation_statements=@readonly.sql \
  default_ttl=1h \
  max_ttl=24h
```

---

## Q80. What is the difference between dynamic and static database roles?

### Answer

Dynamic roles create a new credential per request and revoke it when the lease expires.

Static roles manage rotation of a pre-existing named database account according to a schedule.

Dynamic credentials provide stronger isolation and attribution. Static roles support applications that cannot tolerate changing usernames or new accounts.

---

## Q81. How do you protect the database root credential configured in Vault?

### Answer

- Use a dedicated Vault administration account, not the human database superuser.
- Grant only permissions needed to create/revoke managed users.
- restrict network access from Vault nodes.
- rotate the root credential through Vault where supported.
- audit configuration changes.
- limit access to `database/config/*`.
- use TLS.
- monitor failed revocations.
- avoid returning the root password to operators after configuration.

---

## Q82. What happens if database credential revocation fails?

### Answer

Vault records lease revocation errors and may retry according to expiration-manager behavior.

Actions:

- Check database connectivity.
- inspect plugin logs.
- inspect revocation statements.
- verify the account still exists.
- validate permissions.
- manually revoke if required.
- avoid force revocation until orphaned credentials are tracked.
- alert security if expired credentials remain valid.

---

## Q83. How do cloud secrets engines work?

### Answer

Cloud engines can issue short-lived credentials for platforms such as AWS, Azure, or GCP.

The engine may:

- Assume a role.
- create an IAM user/service principal.
- issue a federation token.
- return temporary credentials.
- revoke or expire the cloud identity.

The exact behavior depends on provider and role type.

---

## Q84. What are common risks with cloud dynamic secrets?

### Answer

- Vault's own cloud credential is overprivileged.
- cloud role policy is too broad.
- credential TTL exceeds business need.
- revocation is eventually consistent or unsupported for some credential types.
- credentials are logged.
- multiple cloud accounts share one mount without clear boundaries.
- IAM quota exhaustion.
- rate limiting.
- incorrect region or account.
- broken cleanup creates orphan identities.

---

## Q85. How do you troubleshoot dynamic secret issuance?

### Answer

Check:

- Mount and role.
- Vault policy.
- backend connection/configuration.
- root credential validity.
- target-system network.
- plugin version.
- creation statement or cloud IAM permissions.
- target-system quota.
- audit log.
- Vault logs.
- client namespace.
- lease creation.

Use a controlled test role with minimum privilege.

---

# 9. Transit, PKI, SSH, TOTP, and Specialized Engines

## Q86. What is the Transit secrets engine?

### Answer

Transit provides cryptographic operations without returning the encryption key to the application.

Capabilities can include:

- Encrypt/decrypt.
- sign/verify.
- HMAC.
- hashing.
- key derivation.
- key rotation.
- rewrap.
- convergent encryption under carefully controlled settings.
- managed keys or BYOK-related capabilities depending on edition/version.

Vault stores the cryptographic key; the application stores ciphertext.

---

## Q87. What is ciphertext rewrap?

### Answer

Rewrap decrypts and re-encrypts ciphertext internally using the latest key version without exposing plaintext to the client.

```bash
vault write transit/rewrap/payments \
  ciphertext="vault:v1:..."
```

Use after key rotation to move stored ciphertext to a newer key version.

---

## Q88. How does Transit key rotation work?

### Answer

```bash
vault write -f transit/keys/payments/rotate
```

Rotation creates a new key version.

Old ciphertext remains decryptable if the minimum decryption version permits it.

Controls include:

- `min_decryption_version`
- `min_encryption_version`
- deletion allowance
- exportability
- auto-rotation period

Do not raise minimum decryption version until old ciphertext has been rewrapped or intentionally retired.

---

## Q89. What is convergent encryption?

### Answer

Convergent encryption can produce deterministic ciphertext for matching plaintext under a controlled context.

This may enable equality comparison or deduplication, but leaks equality patterns.

Use only when required and threat-modeled. Randomized encryption is safer for most data.

---

## Q90. How does the PKI secrets engine work?

### Answer

Vault PKI can:

- Generate or import CA keys.
- create root and intermediate issuers.
- define roles.
- issue certificates.
- revoke certificates.
- publish CA and CRL URLs.
- support OCSP and ACME capabilities depending on configuration/version.
- rotate issuers.

Typical hierarchy:

- Offline or strongly protected root CA.
- Vault-hosted intermediate CA per environment or trust domain.
- Short-lived leaf certificates.

---

## Q91. Why should the root CA normally remain offline?

### Answer

The root CA is the ultimate trust anchor.

Keeping it offline reduces exposure to:

- Vault compromise.
- network attacks.
- operational mistakes.
- unauthorized signing.
- plugin vulnerabilities.

Vault can run an intermediate CA signed by the offline root.

Some architectures intentionally use an online root, but the increased risk must be accepted.

---

## Q92. What is a PKI role?

### Answer

A PKI role controls certificate issuance constraints such as:

- Allowed domains.
- subdomains.
- URI SANs.
- IP SANs.
- key types.
- key sizes.
- TTL.
- server/client usage.
- wildcard use.
- subject fields.
- issuer reference.

A role should not allow arbitrary common names or SANs unless that authority is intentionally delegated.

---

## Q93. How do you rotate a Vault PKI intermediate?

### Answer

1. Generate/import a new intermediate key and CSR.
2. have the parent CA sign it.
3. import the signed certificate.
4. configure issuer names and default/follow-latest behavior.
5. test issuance.
6. distribute the new chain.
7. maintain overlap with old issuer.
8. update roles.
9. monitor clients.
10. retire old issuer only after all certificates and chains are safe.

Issuer rotation must account for CRL and chain publication.

---

## Q94. What is the SSH secrets engine?

### Answer

SSH engine patterns include:

- Signed SSH certificates.
- one-time passwords, where used.

SSH certificate signing is normally preferred because it avoids distributing long-lived host/user keys and supports short-lived identity-bound access.

Policies and roles control:

- principals.
- extensions.
- critical options.
- TTL.
- key types.
- source addresses.

---

## Q95. What is the TOTP secrets engine?

### Answer

The TOTP engine can generate time-based one-time passwords or manage keys for systems using TOTP.

Use cases:

- MFA automation.
- managed TOTP codes for approved integrations.

Protect TOTP seed material and audit access. A generated code is highly sensitive for its validity window.

---

# 10. Kubernetes and OpenShift Integration

## Q96. What are the main ways Kubernetes workloads consume Vault secrets?

### Answer

Major patterns include:

- Vault Agent Injector sidecars/init containers.
- Vault Secrets Store CSI Provider.
- Vault Secrets Operator.
- Vault Secrets Operator CSI driver/protected secrets where licensed and supported.
- Direct application SDK/API.
- External Secrets-style integrations.
- CI rendering into Kubernetes Secrets, generally with greater exposure.

Selection depends on:

- secret storage location.
- rotation behavior.
- Kubernetes Secret requirements.
- per-pod versus cluster-wide Vault connections.
- application reload behavior.
- OpenShift security controls.
- scale.
- audit and threat model.

---

## Q97. What is Vault Agent Injector?

### Answer

The Injector is a mutating admission webhook that adds Vault Agent containers and volumes to annotated pods.

Typical annotations:

```yaml
vault.hashicorp.com/agent-inject: "true"
vault.hashicorp.com/role: "payments"
vault.hashicorp.com/agent-inject-secret-config: "secret/data/payments/config"
```

The Agent authenticates to Vault and renders secrets to an in-memory volume using templates.

---

## Q98. What is the Vault CSI Provider?

### Answer

The Vault Secrets Store CSI Provider integrates with the Kubernetes Secrets Store CSI Driver.

A `SecretProviderClass` specifies which Vault objects to mount.

The secret appears in an ephemeral CSI volume.

Considerations:

- Node-level CSI components.
- `hostPath` requirements.
- mount-time dependency on Vault.
- rotation behavior.
- Kubernetes Secret synchronization if enabled.
- OpenShift SCC restrictions.
- failure behavior during pod start.

---

## Q99. What is Vault Secrets Operator?

### Answer

Vault Secrets Operator watches custom resources and synchronizes Vault secrets into Kubernetes Secrets or supports other integration modes depending on version and features.

Benefits:

- Native Kubernetes Secret consumption.
- centralized connection pools.
- automated refresh.
- rollout/restart triggers.
- lower per-pod Vault connection count than sidecar patterns.

Risk:

- Secret material is copied into Kubernetes Secrets.
- etcd encryption, RBAC, backup, and node/pod access must be secured.

---

## Q100. Injector versus CSI versus VSO: how do you choose?

### Answer

### Agent Injector

Use when:

- Files/templates are needed.
- per-pod identity and independent renewal are valuable.
- application can read mounted files.
- sidecar overhead is acceptable.

### CSI Provider

Use when:

- Ephemeral mounted files are preferred.
- sidecars are undesirable.
- node-level CSI is acceptable.
- application reads files.

### Vault Secrets Operator

Use when:

- Native Kubernetes Secrets are required.
- centralized refresh is useful.
- secret lifecycle should be independent of pod lifecycle.
- Kubernetes Secret exposure is accepted.

At high pod counts, shared operator-based connections generally scale better than one Vault connection per pod.

---

## Q101. How does OpenShift affect Vault integrations?

### Answer

Check:

- SCC permissions.
- restricted Pod Security.
- `hostPath` restrictions for CSI.
- UID ranges.
- SELinux labels.
- Route and Service TLS.
- OpenShift service CA.
- proxy configuration.
- disconnected registry.
- operator lifecycle.
- NetworkPolicy.
- service-account token audiences.

The Agent Injector uses in-memory volumes and may be simpler than CSI in environments where hostPath is restricted, but test against the specific OpenShift version and SCC design.

---

## Q102. How do you secure a Kubernetes auth role?

### Answer

- Exact ServiceAccount names.
- exact namespaces.
- projected short-lived JWT.
- explicit audience.
- short Vault token TTL.
- minimal policy.
- separate roles by workload.
- no binding to `default`.
- separate auth mounts for independent clusters or trust domains.
- audit role changes.
- protect workload ServiceAccount creation and token access.

---

## Q103. How do you handle secret rotation in Kubernetes?

### Answer

The integration retrieves or refreshes the new value, but the application must consume it.

Patterns:

- Application watches a mounted file.
- sidecar sends a signal.
- pod is restarted.
- operator updates Secret and triggers rollout.
- application retrieves directly per request or on a timer.

Define:

- maximum stale-secret interval.
- reload behavior.
- rollback.
- connection-pool handling.
- outage behavior.
- coordination with external credential revocation.

---

# 11. Vault Agent, Injector, CSI, and Secrets Operator

## Q104. What is Vault Agent?

### Answer

Vault Agent is a client-side process that can provide:

- Auto-authentication.
- token renewal.
- secret templating.
- caching/proxy behavior.
- process supervision in supported patterns.
- sink delivery.

It reduces the amount of Vault logic required in applications.

---

## Q105. What are Auto-Auth method and sink?

### Answer

Auto-Auth:

1. Uses a configured auth method.
2. obtains a Vault token.
3. writes the token to one or more sinks.
4. renews or re-authenticates.

Example sink types can include files.

Protect sinks with:

- restrictive filesystem permissions.
- memory-backed storage.
- encryption/wrapping where supported.
- short token TTL.
- no shared writable directory.

---

## Q106. What is Vault Agent caching?

### Answer

Agent caching can reduce direct requests to Vault and help manage tokens and leased secrets.

Benefits:

- Lower Vault load.
- local availability for selected cached data.
- simplified application integration.

Risks:

- Secret material exists in Agent memory.
- cache behavior and persistence must be understood.
- stale values.
- shared cache authorization.
- application trust boundary.

---

## Q107. What are common Injector troubleshooting checks?

### Answer

```bash
kubectl get pods -n vault
kubectl get mutatingwebhookconfigurations
kubectl logs -n vault deploy/vault-agent-injector
kubectl describe pod <pod> -n <namespace>
kubectl logs <pod> -c vault-agent-init
kubectl logs <pod> -c vault-agent
```

Check:

- Injection annotation.
- webhook namespace/object selector.
- Service endpoints.
- TLS CA bundle.
- ServiceAccount.
- Kubernetes auth role.
- policy.
- Vault address.
- CA certificate.
- template syntax.
- secret path.
- token audience.
- NetworkPolicy.

---

## Q108. Why can injected secrets be missing even when the sidecar is running?

### Answer

- Wrong template destination.
- wrong secret path.
- KV v2 path misunderstanding.
- token lacks read permission.
- template rendering error.
- secret not yet written.
- namespace mismatch.
- file permissions.
- application starts before rendering.
- sidecar failed after initial authentication.
- renewal/re-auth failure.

Inspect both init and sidecar logs and verify the exact rendered path.

---

# 12. Namespaces, Replication, and Multi-Cluster Design

## Q109. What are Vault Enterprise namespaces?

### Answer

Namespaces provide secure multi-tenancy within one Vault Enterprise cluster.

Each namespace can have:

- Auth methods.
- secrets engines.
- policies.
- identity configuration.
- child namespaces.
- delegated administration.

A namespace behaves like a Vault environment under a path-like administrative hierarchy.

---

## Q110. How is namespace context specified?

### Answer

CLI:

```bash
export VAULT_NAMESPACE=finance/payments
vault status
```

API header:

```text
X-Vault-Namespace: finance/payments
```

A missing or incorrect namespace can produce:

- permission denied.
- no value found.
- wrong mount.
- login failure.
- policy mismatch.

Always capture namespace context in troubleshooting.

---

## Q111. What is performance replication?

### Answer

Performance replication replicates shared Vault data from a primary cluster to performance secondary clusters.

Secondaries can serve many local read and cryptographic requests. Requests that modify shared state are forwarded to the primary.

Performance replication supports:

- Geographic read scaling.
- lower latency.
- workload distribution.
- local mounts for region-specific data.

It is not a replacement for DR replication or snapshots.

---

## Q112. What is disaster-recovery replication?

### Answer

DR replication keeps a secondary cluster synchronized for catastrophic primary-cluster failure.

A DR secondary:

- Does not normally serve client read/write traffic.
- receives replicated configuration and state.
- can be promoted during disaster recovery.
- is designed to continue operations with the same token and lease infrastructure after promotion.

Promotion requires a controlled DR operation-token workflow.

---

## Q113. Performance replication versus DR replication?

### Answer

| Feature | Performance Replication | DR Replication |
|---|---|---|
| Primary purpose | Scale and regional latency | Catastrophic recovery |
| Serves normal client traffic | Yes, within supported behavior | No until promoted |
| Shared data replication | Yes | Yes |
| Local mounts | Remain local to performance cluster; replicate to DR paired with that cluster as designed | Replicated from paired primary |
| Tokens/leases | Generally local behavior for performance clusters | Replicated for continuity |
| Promotion | Performance secondary promotion workflow | DR promotion workflow |
| Backup replacement | No | No |

Exact replication categories and local/shared behavior must be reviewed for the installed version.

---

## Q114. What are local mounts?

### Answer

A local auth method or secrets-engine mount is not replicated to performance replication peers, but is replicated to the corresponding DR secondary.

Enable:

```bash
vault secrets enable -local -path=regional-kv kv-v2
vault auth enable -local approle
```

Use for:

- Regional cloud credentials.
- data-residency constraints.
- local workload identities.
- local high-volume secrets.

Document failover implications because another performance cluster may not contain the local data.

---

## Q115. What is a replication path filter?

### Answer

Path filters control which mounts or namespaces are replicated to selected performance secondaries.

Use cases:

- Data sovereignty.
- regional isolation.
- reducing unnecessary replication.
- limiting sensitive datasets.

Incorrect filters can cause applications to miss secrets after traffic shifts. Test failover and promotion behavior.

---

## Q116. What is eventual consistency in replicated Vault?

### Answer

A write accepted on the active/primary may not be visible immediately on a performance standby or secondary.

Clients requiring read-after-write consistency must use supported consistency controls, forwarding behavior, or read from the authoritative cluster.

Common symptoms:

- Secret written, immediate regional read returns old version.
- Policy change not yet active on secondary.
- Identity update delayed.

Do not treat every temporary stale read as data loss.

---

## Q117. How do you design Vault across multiple regions?

### Answer

Consider:

- One performance primary.
- performance secondaries near workloads.
- DR secondaries for each critical cluster.
- local mounts for regional dependencies.
- path filters for residency.
- independent seal/KMS per region where supported.
- global DNS/load balancing.
- client consistency needs.
- replication bandwidth.
- promotion procedures.
- snapshots per cluster.
- license and version compatibility.
- regional operator access.

---

# 13. Audit, Telemetry, Performance, and Capacity

## Q118. Why are Vault audit devices critical?

### Answer

Audit devices record requests and responses in a security-focused format.

They support:

- Forensics.
- compliance.
- access review.
- root-activity detection.
- policy debugging.
- credential issuance tracking.
- incident response.

Production Vault should have at least one reliable audit device, and critical environments commonly use more than one independent destination.

---

## Q119. What information is HMAC-hashed in audit logs?

### Answer

Vault hashes many sensitive string values using a device-specific HMAC key so they can be correlated without exposing plaintext.

Some fields are not HMACed because they are operationally required. Selected fields can be configured as non-HMAC keys for supported audit devices.

Do not assume audit logs contain no sensitive information. Protect paths, identity metadata, IP addresses, and non-HMAC fields.

---

## Q120. What happens if all audit devices fail?

### Answer

Vault generally refuses requests that require audit logging if it cannot write to any enabled audit device.

This is a security feature: Vault avoids processing unaudited operations.

Operationally, full disks or unavailable syslog endpoints can therefore cause a production outage.

Monitor:

- Disk capacity.
- file permissions.
- inode usage.
- remote sink availability.
- write latency.
- log rotation.
- Vault audit errors.

---

## Q121. How do you enable a file audit device?

### Answer

```bash
vault audit enable file \
  file_path=/var/log/vault/audit.log
```

Use:

- Dedicated filesystem.
- restrictive permissions.
- log rotation that safely reopens/reloads.
- monitoring.
- encrypted transport to central logging.
- tamper protection.
- retention policy.

Do not place audit logs on the same small filesystem as Raft data.

---

## Q122. What Vault telemetry should be monitored?

### Answer

Monitor:

- Request rate.
- request latency.
- response status.
- token creation and expiration.
- lease counts.
- audit failures.
- seal status.
- active/standby state.
- Raft peer health.
- Raft commit/apply performance.
- storage latency.
- leadership changes.
- replication lag.
- WAL/queue behavior.
- plugin failures.
- runtime memory and garbage collection.
- CPU.
- network.
- file descriptors.

---

## Q123. How do you troubleshoot slow Vault requests?

### Answer

1. Determine affected API path and operation.
2. compare active, standby, and regional cluster latency.
3. check storage latency.
4. inspect audit-device write latency.
5. inspect backend dependency: database, cloud, KMS, LDAP, registry.
6. check CPU throttling and memory.
7. inspect Raft leader and network.
8. inspect request rate and concurrency.
9. use audit request IDs for correlation.
10. check token/entity/group policy complexity.
11. review performance standby and forwarding behavior.
12. capture p50/p95/p99, not only averages.

---

## Q124. Why can audit logging reduce Vault performance?

### Answer

Every request must be written to enabled audit devices.

Slow audit storage or network sinks can add latency.

Controls:

- Fast dedicated disk.
- independent devices.
- asynchronous external collection after reliable local write.
- correct log rotation.
- adequate IOPS.
- avoid blocking remote endpoints without local buffering.
- performance testing with realistic audit configuration.

Never disable auditing merely to make a benchmark look faster.

---

## Q125. What limits Vault throughput?

### Answer

Potential bottlenecks:

- Storage IOPS/latency.
- active-node CPU.
- audit devices.
- external backend latency.
- seal/HSM/KMS latency.
- network.
- TLS.
- policy and identity evaluation.
- large responses.
- Raft replication.
- plugin behavior.
- token/lease churn.
- memory and garbage collection.
- client connection patterns.

Vault HA improves availability; it does not automatically make every write path horizontally scalable.

---

## Q126. How do you capacity-plan Vault?

### Answer

Measure:

- Requests per second by endpoint.
- read/write ratio.
- p95/p99 latency.
- token creation rate.
- active leases.
- secret size.
- PKI issuance rate.
- transit operations.
- Raft database growth.
- snapshot duration and size.
- audit volume.
- replication bandwidth.
- failover load.
- peak authentication storms.
- Kubernetes pod churn.

Test failover with one active node unavailable and during audit/backup activity.

---

# 14. Security Hardening and Enterprise Operations

## Q127. What are essential Vault production-hardening controls?

### Answer

- Run as a dedicated unprivileged user.
- End-to-end TLS.
- disable swap.
- disable core dumps.
- protect configuration and binary from Vault-user modification.
- isolate Vault from unrelated workloads.
- use restricted firewall/NetworkPolicy.
- secure storage.
- enable reliable audit devices.
- use short-lived credentials.
- protect seal keys.
- remove root tokens.
- least-privilege admin policies.
- patch and upgrade.
- monitor and alert.
- tested backups.
- time synchronization.
- secure plugin directory.
- restrict shell and filesystem access.

---

## Q128. Why should swap and core dumps be disabled?

### Answer

Vault holds decrypted secrets and key material in memory while operating.

Swap may write memory pages to disk.

Core dumps may capture process memory.

Controls on Linux:

```ini
# systemd override
[Service]
LimitCORE=0
```

Also configure OS/container memory controls appropriately and verify the effective limit.

---

## Q129. How do you harden the Vault systemd service?

### Answer

Possible controls, tested for compatibility:

```ini
[Service]
User=vault
Group=vault
LimitMEMLOCK=infinity
LimitCORE=0
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/opt/vault/data /var/log/vault
CapabilityBoundingSet=CAP_IPC_LOCK
AmbientCapabilities=CAP_IPC_LOCK
```

Do not copy hardening directives blindly. Test plugin execution, TLS file access, audit paths, and integrated storage permissions.

---

## Q130. How do you secure Vault TLS?

### Answer

- Trusted enterprise or public CA.
- correct SANs.
- TLS on every hop.
- modern protocols/ciphers.
- separate client and cluster concerns.
- automated renewal.
- overlapping certificate rollout.
- private-key protection.
- certificate-expiry monitoring.
- validate load-balancer health behavior.
- avoid `tls_skip_verify`.
- enable HSTS/custom headers where appropriate.
- use mTLS for administrative or machine paths when required.

---

## Q131. How do you secure auto-unseal KMS?

### Answer

- Dedicated KMS key.
- deletion protection.
- strong IAM.
- service control policies.
- key rotation compatible with Vault behavior.
- multi-region design where appropriate.
- audit KMS calls.
- monitor denial/throttling.
- protect credentials.
- prevent accidental scheduled deletion.
- document KMS outage behavior.
- test disaster recovery.
- keep independent secondary seal designs where supported.

---

## Q132. How do you manage Vault administrative access?

### Answer

- SSO/OIDC or strong enterprise auth.
- MFA.
- separate operator and application auth mounts.
- least-privilege admin policies.
- control groups for critical operations.
- short token TTL.
- no shared accounts.
- break-glass generate-root.
- session/audit review.
- privileged access workstation.
- change tickets.
- segregation of duties.
- periodic access recertification.

---

## Q133. How do you manage plugins securely?

### Answer

- Use built-in plugins when appropriate.
- pin versions and checksums.
- store binaries in a protected plugin directory.
- prevent Vault service account from modifying binaries.
- register approved checksum.
- review source/vendor.
- patch vulnerabilities.
- test upgrade and rollback.
- restrict plugin process permissions.
- monitor crashes.
- back up plugin catalog and binaries.
- avoid arbitrary unreviewed plugins.

---

# 15. Backup, Restore, Disaster Recovery, and Upgrades

## Q134. What must be backed up for Vault?

### Answer

Depending on architecture:

- Raft snapshots or supported storage backup.
- Vault configuration.
- TLS certificates and private-key recovery method.
- plugins and plugin catalog.
- Helm/Terraform/systemd deployment configuration.
- audit configuration.
- load-balancer and DNS configuration.
- auto-unseal/HSM configuration and key availability.
- license.
- runbooks.
- external identity/auth configuration.
- replication topology.
- policies as code.

Audit logs should be backed up separately according to retention policy.

---

## Q135. How do you validate a snapshot backup?

### Answer

- Check command success.
- inspect snapshot metadata.
- verify file size and checksum.
- encrypt and upload to independent storage.
- test restore in an isolated environment.
- verify unseal.
- verify mounts, policies, auth, identity, PKI, and leases.
- verify applications with test credentials.
- record restore duration.
- monitor backup age.

A backup not tested through restore is only an assumption.

---

## Q136. What is the safe restore process?

### Answer

1. Declare recovery event and freeze writes.
2. preserve the failed/current storage state.
3. identify the correct snapshot.
4. validate seal/KMS/HSM availability.
5. build isolated replacement infrastructure.
6. restore using supported procedure.
7. unseal and verify Raft/cluster status.
8. validate critical paths.
9. reconcile credentials issued after snapshot time.
10. rotate potentially duplicated dynamic credentials.
11. switch traffic.
12. retain evidence and complete post-incident review.

A point-in-time restore can reintroduce revoked tokens or credentials and lose newer revocation records. Perform security reconciliation.

---

## Q137. What should an upgrade plan include?

### Answer

- Supported source and target versions.
- required intermediate versions.
- release notes and breaking changes.
- plugin compatibility.
- Enterprise replication order.
- snapshot.
- configuration backup.
- canary/standby upgrade.
- Autopilot health.
- rollback boundaries.
- performance baseline.
- audit and monitoring validation.
- API/client compatibility.
- maintenance communications.
- post-upgrade policy/auth/secrets tests.

---

## Q138. What is the rolling upgrade sequence for an HA Raft cluster?

### Answer

A general pattern:

1. Validate snapshot and quorum.
2. upgrade standby nodes one at a time.
3. unseal/join and verify each node.
4. confirm Autopilot health.
5. step down the active node.
6. allow an upgraded standby to become active.
7. upgrade the former active.
8. verify all peers, versions, logs, metrics, and APIs.

Follow version-specific official guidance, especially for major upgrades and Enterprise replication.

---

## Q139. How do you upgrade replicated Enterprise clusters?

### Answer

A general principle is to upgrade secondary clusters before the primary so secondaries may temporarily run a newer compatible version.

Do not keep mixed versions longer than necessary.

Validate:

- DR and performance topology.
- promotion capability.
- seal compatibility.
- replication status.
- local mounts.
- plugins.
- namespaces.
- performance standby behavior.
- rollback limits.

Use the exact version-specific upgrade procedure.

---

## Q140. Why is rollback after a Vault upgrade risky?

### Answer

A newer Vault version may change:

- Storage schema.
- Raft protocol behavior.
- plugin data.
- identity representation.
- API behavior.
- replication state.
- seal-wrapped data.

Binary downgrade may be unsupported or unsafe after migrations.

Rollback may require restoring a pre-upgrade snapshot, which introduces data-loss and credential-reconciliation consequences.

---

# 16. Corporate L3 Troubleshooting Scenarios

## Q141. Vault is sealed after a restart despite auto-unseal. What do you check?

### Answer

Check:

```bash
vault status
journalctl -u vault -b
```

Investigate:

- KMS/HSM availability.
- IAM credentials.
- key ID and region.
- network/DNS.
- proxy.
- KMS key disabled or scheduled for deletion.
- throttling.
- seal stanza.
- environment variables.
- cloud instance identity.
- certificate/time issues.
- recent seal migration.

Do not initialize Vault again.

---

## Q142. Vault reports “permission denied.” How do you troubleshoot?

### Answer

1. Capture exact command and path.
2. identify namespace.
3. inspect token:

```bash
vault token lookup
vault token capabilities <path>
```

4. inspect policies.
5. inspect KV v2 API path.
6. check identity groups.
7. check Sentinel/control groups.
8. inspect audit entry.
9. confirm auth mount.
10. test with the same token, not an administrator token.

---

## Q143. No active Vault node is elected. What do you check?

### Answer

- Number of healthy Raft voters.
- network on port 8201.
- storage latency.
- node IDs.
- seal state.
- TLS.
- clock.
- Raft logs.
- peer list.
- recent membership changes.
- failed zones.
- disk full or read-only filesystem.
- address configuration.
- quorum.

Preserve snapshots before peer recovery actions.

---

## Q144. Vault latency suddenly increases. What do you investigate?

### Answer

- Storage latency and disk queue.
- audit filesystem.
- active-node CPU/memory.
- KMS/HSM latency.
- database/cloud/LDAP backend.
- network/TLS.
- leadership changes.
- replication lag.
- request spike.
- authentication storm.
- expensive policies/identity groups.
- large list responses.
- plugin errors.
- file descriptors.
- backup activity.
- recent version/configuration change.

---

## Q145. Vault is returning 503. What can it mean?

### Answer

Possible meanings include:

- Sealed.
- standby with response mode/health-code behavior.
- no active leader.
- initialization incomplete.
- replication state.
- overloaded or unavailable backend.
- health check configuration.

Inspect the JSON body and `/sys/health` query options rather than relying only on status code.

---

## Q146. A database dynamic credential remains valid after lease expiry. What do you do?

### Answer

- Inspect lease and expiration logs.
- test database connectivity from Vault.
- inspect revocation statements.
- confirm database privileges.
- check plugin timeout/error.
- manually disable the account.
- assess other expired leases.
- alert security.
- repair revocation.
- avoid force-revoking until orphaned accounts are tracked.
- add monitoring for revocation errors.

---

## Q147. Kubernetes pods receive “service account unauthorized.” What do you check?

### Answer

- ServiceAccount name.
- namespace.
- role bindings.
- audience.
- projected token.
- auth mount path.
- Vault role.
- token reviewer configuration.
- Kubernetes CA.
- issuer.
- cluster API endpoint.
- time synchronization.
- pod uses intended ServiceAccount.
- Vault policy.

Decode JWT claims carefully without logging the full token.

---

## Q148. Vault Agent Injector does not mutate pods. What do you check?

### Answer

- Injector pods.
- webhook configuration.
- Service and endpoints.
- CA bundle.
- namespace selector.
- pod annotation.
- API server reachability.
- certificate.
- failurePolicy.
- pod already created before annotation.
- webhook conflict.
- OpenShift SCC/admission.
- injector logs.

---

## Q149. A Raft snapshot restore completed, but applications fail. Why?

### Answer

Possible causes:

- Auth-provider configuration changed after snapshot.
- KMS/TLS/DNS mismatch.
- policies reverted.
- application credentials were issued after snapshot.
- database accounts no longer match leases.
- Kubernetes roles/service accounts changed.
- PKI CRLs/issuers reverted.
- replication state mismatch.
- namespaces or mounts differ.
- load balancer points to old cluster.

Restore requires application and external-system reconciliation.

---

## Q150. Audit logs stop and Vault requests fail. What do you do?

### Answer

- Check all audit device status.
- disk/inodes.
- file permissions.
- syslog/network endpoint.
- log rotation.
- Vault logs.
- filesystem read-only state.
- audit-device latency.
- restore at least one reliable device.
- validate audit continuity.
- investigate whether any requests were processed without expected records.

Do not disable every audit device merely to restore traffic unless an approved emergency process accepts the compliance risk.

---

## Q151. Performance secondary returns stale data. Is that a failure?

### Answer

Not necessarily. Performance replication and performance standbys can exhibit eventual consistency.

Check:

- Time since write.
- replication status.
- cluster state.
- consistency requirements.
- client headers/tokens supported for read-your-writes behavior.
- whether the mount is local.
- path filter.
- write occurred on the expected primary.
- replication lag.

If strict read-after-write is required, route appropriately.

---

## Q152. A root or highly privileged token is exposed. What do you do?

### Answer

1. Revoke token by value or accessor immediately.
2. capture audit evidence.
3. identify actions performed.
4. rotate secrets accessed or modified.
5. inspect policy/auth/mount changes.
6. check for child or orphan tokens.
7. examine new entities, groups, and tokens.
8. preserve logs.
9. invoke security incident response.
10. correct the control failure.
11. generate a temporary root only if required for recovery and revoke it afterward.

---

## Q153. Vault's Raft disk is almost full. What do you do?

### Answer

- Check filesystem and inode usage.
- inspect Raft database/snapshot size.
- identify secret and lease growth.
- inspect PKI certificates, KV versions, identity, tokens, and audit location.
- expand storage safely.
- take snapshot.
- review KV max versions.
- revoke stale leases.
- remove unnecessary data through supported APIs.
- review snapshot retention outside the data directory.
- do not manually delete Raft files.

---

## Q154. Vault is using high memory. What do you check?

### Answer

- Request concurrency.
- large secret values/responses.
- identity/entity/group count.
- token and lease volume.
- Raft cache and storage growth.
- plugin processes.
- telemetry.
- Go heap and GC.
- connection count.
- performance standby workload.
- recent version.
- memory limits/OOM.
- audit pipeline.
- debug profiling under approved guidance.

Do not capture heap/core data without handling it as highly sensitive.

---

## Q155. Authentication works, but secrets cannot be read. Why?

### Answer

Authentication only proves identity and issues a token.

Read failure may be caused by:

- No policy.
- wrong path.
- KV v2 path.
- namespace mismatch.
- entity/group mapping.
- explicit deny.
- Sentinel.
- expired token.
- missing `list` versus `read`.
- mount not present.
- token created before policy change with unexpected identity behavior.

---

## Q156. How do you handle a suspected Raft corruption incident?

### Answer

- Stop unsafe writes and maintenance.
- preserve disk images/snapshots.
- capture logs and peer state.
- verify hardware/storage errors.
- contact HashiCorp support for Enterprise or expert assistance.
- avoid manually modifying Bolt/Raft files.
- identify latest validated snapshot.
- rebuild an isolated cluster.
- reconcile post-snapshot credentials.
- complete storage/root-cause review.

---

# 17. Rapid-Fire Commands and Diagnostic Checklists

## Environment

```bash
export VAULT_ADDR=https://vault.example.com:8200
export VAULT_CACERT=/etc/pki/ca-trust/source/anchors/vault-ca.pem
export VAULT_NAMESPACE=finance/payments
```

Avoid placing `VAULT_TOKEN` in shared shell profiles.

## Health and Status

```bash
vault version
vault status
vault read sys/health
curl --cacert "$VAULT_CACERT" "$VAULT_ADDR/v1/sys/health"
```

## Initialization and Seal

```bash
vault operator init
vault operator unseal
vault operator seal
vault operator rekey
vault operator generate-root -init
```

## Raft

```bash
vault operator raft list-peers
vault operator raft autopilot state
vault operator raft join https://vault-1.example.com:8200
vault operator raft remove-peer <node-id>
vault operator raft snapshot save vault.snap
vault operator raft snapshot inspect vault.snap
vault operator raft snapshot restore vault.snap
vault operator step-down
```

## Mounts and Auth

```bash
vault secrets list -detailed
vault auth list -detailed
vault secrets enable -path=secret kv-v2
vault auth enable kubernetes
vault auth enable approle
vault secrets tune -default-lease-ttl=1h -max-lease-ttl=24h database/
```

## Policies and Tokens

```bash
vault policy list
vault policy read payments
vault policy write payments payments.hcl
vault token lookup
vault token capabilities secret/data/payments/config
vault token create -policy=payments -ttl=30m
vault token revoke <token>
vault token revoke -accessor <accessor>
```

## Leases

```bash
vault lease lookup <lease-id>
vault lease renew <lease-id>
vault lease revoke <lease-id>
vault lease revoke -prefix database/creds/payments/
```

## KV v2

```bash
vault kv put secret/payments/config username=app password=example
vault kv get secret/payments/config
vault kv get -version=2 secret/payments/config
vault kv delete secret/payments/config
vault kv undelete -versions=2 secret/payments/config
vault kv destroy -versions=1 secret/payments/config
vault kv metadata get secret/payments/config
```

## Audit

```bash
vault audit list -detailed
vault audit enable file file_path=/var/log/vault/audit.log
vault audit disable file/
```

## Identity

```bash
vault list identity/entity/id
vault read identity/entity/id/<id>
vault list identity/group/id
vault read identity/group/id/<id>
```

## Debugging

```bash
journalctl -u vault --since=-30m
systemctl status vault
ss -lntp | grep -E '8200|8201'
openssl s_client -connect vault.example.com:8200 \
  -servername vault.example.com -showcerts
df -h
df -i
free -m
vmstat 1
iostat -xz 1
```

## Kubernetes

```bash
kubectl get pods -n vault -o wide
kubectl logs -n vault statefulset/vault
kubectl get mutatingwebhookconfigurations
kubectl get secretproviderclass -A
kubectl get vaultauth,vaultconnection,vaultstaticsecret -A
kubectl describe pod <pod> -n <namespace>
```

---

# 18. Senior Interview Response Framework

For every Vault production scenario, answer in this order:

1. **Identify the trust boundary:** Which cluster, namespace, auth mount, secret mount, and external system are involved?
2. **Preserve evidence:** Capture request ID, audit entry, logs, token accessor, lease ID, Raft status, and configuration version.
3. **Assess blast radius:** Applications, credentials, certificates, regions, replication peers, and data residency.
4. **Contain safely:** Revoke, disable a role, isolate traffic, step down, or roll back only the faulty component.
5. **Preserve quorum and seal access:** Never perform storage or peer changes without understanding recovery.
6. **Validate recovery:** Test authentication, policy, secret issuance, renewal, revocation, audit, and application behavior.
7. **Reconcile external systems:** Database users, cloud identities, certificates, and post-snapshot credentials.
8. **Prevent recurrence:** Least privilege, tests, alerts, backup drills, access review, and documented runbooks.
9. **Communicate:** Record exact paths, object IDs, token accessors, lease IDs, owners, timeline, and risk acceptance.

### Strong L3 interview language

- “I will distinguish authentication failure from authorization failure.”
- “I will capture the exact namespace, mount path, token accessor, and request ID.”
- “I will not initialize Vault when an existing cluster is sealed.”
- “Recovery keys cannot replace a lost auto-unseal KMS key.”
- “HA is not a backup, and Raft replication is not geographic disaster recovery.”
- “I will preserve Raft quorum before changing peer membership.”
- “I will test revocation in the target database, not only remove the lease from Vault.”
- “A restored snapshot requires reconciliation of credentials issued after the snapshot.”
- “I will revoke the exposed credential first, then perform history and audit analysis.”
- “The root token is a break-glass mechanism, not a daily administrator credential.”

---

# 19. Official References

- Vault documentation: https://developer.hashicorp.com/vault/docs
- Vault releases: https://github.com/hashicorp/vault/releases
- Architecture concepts: https://developer.hashicorp.com/vault/docs/internals
- Seal and unseal: https://developer.hashicorp.com/vault/docs/concepts/seal
- Production hardening: https://developer.hashicorp.com/vault/docs/concepts/production-hardening
- Integrated storage: https://developer.hashicorp.com/vault/docs/concepts/integrated-storage
- Raft internals: https://developer.hashicorp.com/vault/docs/internals/integrated-storage
- High availability: https://developer.hashicorp.com/vault/docs/concepts/ha
- Server HA internals: https://developer.hashicorp.com/vault/docs/internals/high-availability
- Policies: https://developer.hashicorp.com/vault/docs/concepts/policies
- Tokens: https://developer.hashicorp.com/vault/docs/concepts/tokens
- Lease, renew, and revoke: https://developer.hashicorp.com/vault/docs/concepts/lease
- Authentication methods: https://developer.hashicorp.com/vault/docs/auth
- Secrets engines: https://developer.hashicorp.com/vault/docs/secrets
- Audit devices: https://developer.hashicorp.com/vault/docs/audit
- Kubernetes deployment: https://developer.hashicorp.com/vault/docs/deploy/kubernetes
- Kubernetes integration comparison: https://developer.hashicorp.com/vault/docs/deploy/kubernetes/comparisons
- Vault Agent Injector: https://developer.hashicorp.com/vault/docs/deploy/kubernetes/injector
- Vault CSI Provider: https://developer.hashicorp.com/vault/docs/deploy/kubernetes/csi
- Vault Secrets Operator: https://developer.hashicorp.com/vault/docs/deploy/kubernetes/vso
- Enterprise namespaces: https://developer.hashicorp.com/vault/docs/enterprise/namespaces
- Enterprise replication: https://developer.hashicorp.com/vault/docs/enterprise/replication
- Eventual consistency: https://developer.hashicorp.com/vault/docs/enterprise/consistency
- Enterprise cluster design: https://developer.hashicorp.com/vault/docs/enterprise/cluster-design
- HSM support: https://developer.hashicorp.com/vault/docs/enterprise/hsm
- Telemetry metrics: https://developer.hashicorp.com/vault/docs/internals/telemetry/metrics/all
- Performance tuning: https://developer.hashicorp.com/vault/docs/concepts/tune-server-performance
- Upgrade guidance: https://developer.hashicorp.com/vault/docs/upgrading
- Troubleshooting: https://developer.hashicorp.com/vault/docs/troubleshoot
- Recovery mode: https://developer.hashicorp.com/vault/docs/concepts/recovery-mode

---

## End of Document
