# Open Policy Agent (OPA) Corporate L3 — Complete Interview Questions and Answers


---

<!-- Original file: 01. OPA Architecture and Enterprise Policy Design.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Architecture and Enterprise Policy Design

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** OPA roles, deployment models, decision contracts, control planes, availability, and enforcement design.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What is Open Policy Agent, and where does it fit in enterprise architecture?

### Detailed answer

OPA is a general-purpose policy decision engine. Applications and infrastructure components send structured input to OPA, OPA evaluates Rego policies and referenced data, and returns a decision such as allow/deny, a set of violations, routing attributes, filters, limits, or configuration.

OPA separates policy decision-making from policy enforcement. The caller remains the policy enforcement point and must correctly interpret and enforce the returned decision. This separation allows a common policy language and lifecycle to be reused across APIs, microservices, Kubernetes, CI/CD, infrastructure-as-code, service meshes, and data access.

### Example / evidence

```text
Typical flow:
1. Enforcement point builds JSON input.
2. OPA evaluates `data.<package>.<decision>`.
3. OPA returns a typed JSON result.
4. Enforcement point applies the result.
5. Decision logs and metrics provide evidence.
```

### L3 follow-up discussion

Explain the policy decision point, policy enforcement point, policy administration point, policy information sources, and why the input/decision schema is an API contract.

### Production caution

OPA does not enforce anything by itself. A correct Rego policy provides no protection if the integrating application ignores errors or misinterprets undefined decisions.

---

## Q2. Why is OPA described as general-purpose?

### Detailed answer

OPA is not tied to one resource type, protocol, or allow/deny model. It evaluates structured documents, normally JSON-compatible values, and can return arbitrary structured decisions. The same engine can decide HTTP authorization, Kubernetes admission violations, Terraform plan compliance, feature entitlements, data filters, or service-mesh attributes.

The common elements are a stable input schema, Rego policy, supporting data, a query entrypoint, and an enforcement integration.

### Example / evidence

```text
Example decision shapes:
- Boolean: `{"result": true}`
- Object: `{"allow": false, "reasons": [...]}`
- Set/list: violations or permitted fields
- Numeric: rate limit or quota
- String/object: routing or configuration decision
```

### L3 follow-up discussion

Discuss why enterprise policy platforms usually standardize decision contracts instead of allowing every team to return an undocumented value.

### Production caution

Using one OPA binary does not automatically create policy consistency. Governance, schemas, libraries, testing, and distribution are still required.

---

## Q3. Compare centralized and distributed OPA deployment models.

### Detailed answer

A centralized OPA service is simpler to operate and govern but adds network latency, creates a shared dependency, and may become a scaling or failure bottleneck. A distributed model places OPA near each enforcement point—as a sidecar, host daemon, library, or local service—providing low-latency decisions and reducing dependency on a remote network call.

Distributed OPA requires a control-plane design for policy and data distribution, status, version rollout, decision telemetry, and fleet observability. Hybrid models are common: logically centralized management with physically distributed evaluators.

### Example / evidence

```text
Centralized questions:
- What is the availability target?
- How is tenant isolation enforced?
- What happens during network partition?

Distributed questions:
- How are bundles promoted?
- How is stale policy detected?
- How are agents inventoried and upgraded?
```

### L3 follow-up discussion

Explain why policy distribution should usually be asynchronous while decision evaluation stays local and synchronous.

### Production caution

A remote OPA call in every user request can turn the policy system into a global outage dependency unless latency, capacity, timeout, and fail behavior are engineered.

---

## Q4. What are policy decision points and policy enforcement points?

### Detailed answer

The policy decision point evaluates policy and produces a decision. OPA is normally the PDP. The policy enforcement point intercepts an operation, constructs input, asks for a decision, and allows, denies, modifies, or filters the operation. Examples of PEPs are an API gateway, application middleware, Envoy external authorization filter, Kubernetes API admission webhook, CI pipeline, or Terraform wrapper.

The boundary matters because authentication context, resource state, error handling, and enforcement semantics are usually implemented by the PEP.

### Example / evidence

```text
A secure PEP must:
- Authenticate or receive trusted identity.
- Construct complete, canonical input.
- Query the intended entrypoint.
- Validate the decision type.
- Apply fail-open/fail-closed policy.
- Record correlation and decision identifiers.
```

### L3 follow-up discussion

Discuss confused-deputy risks when the caller can supply identity fields that should come from a trusted authentication layer.

### Production caution

Do not let clients directly claim roles, tenant IDs, or ownership fields in OPA input unless those values were verified by the enforcement point.

---

## Q5. What is the `input` document versus the `data` document?

### Detailed answer

`input` is the request-specific document supplied for one evaluation. `data` is the base and virtual document namespace. Base documents come from loaded JSON/YAML data and bundles; virtual documents are produced by Rego rules.

Policies read `input` and `data`, and their results appear under `data` according to package and rule names. Keeping request context in input and relatively stable reference data in data improves reuse and caching.

### Example / evidence

```text
Example query:
`data.http.authz.allow`

Possible input:
`{"method":"GET","path":["accounts","123"],"subject":{"id":"u1","roles":["reader"]}}`

Possible data:
`data.roles`, `data.ownership`, or bundle-supplied configuration.
```

### L3 follow-up discussion

Explain why large, rapidly changing application databases should not automatically be copied into OPA data. Consider freshness, memory, distribution cost, and authoritative ownership.

### Production caution

If required input fields are absent, a rule can become undefined rather than false. The decision contract must define missing-data behavior.

---

## Q6. What does an undefined OPA result mean?

### Detailed answer

Undefined means the queried document has no value for the supplied input and data. It is not automatically equivalent to false, deny, empty list, or server error. Undefined often results from a rule whose conditions did not match, a missing input field, or querying the wrong path.

Production integrations should choose entrypoints with explicit defaults or validate undefined responses and convert them according to a documented fail policy.

### Example / evidence

```text
Recommended Boolean pattern:
`default allow := false`

Recommended object pattern:
`default decision := {"allow": false, "reasons": ["no matching policy"]}`

Integration check:
- HTTP 200 with no `result` can represent undefined.
- HTTP transport success is not decision success.
```

### L3 follow-up discussion

Discuss how undefined differs from a compile error, evaluation error, and explicit false result.

### Production caution

An integration that treats missing `result` as allow creates a serious fail-open vulnerability.

---

## Q7. How does OPA maintain availability during bundle updates?

### Detailed answer

OPA evaluates against an in-memory policy and data snapshot. Bundle activation is transactional from the evaluator's perspective: a successfully downloaded and verified bundle is activated as a coherent revision, while the previous active policy remains available if the new bundle cannot be loaded or compiled.

Operationally, the fleet may temporarily contain agents on different revisions, so status reporting, rollout waves, compatibility, and maximum staleness must be governed.

### Example / evidence

```text
Evidence to track:
- Bundle name and revision
- Last successful activation
- Download or compile errors
- Agent version
- Decision log bundle metadata
- Percentage of fleet on target revision
```

### L3 follow-up discussion

Explain backward-compatible changes to input schemas and decision shapes during rolling deployment of callers and policy agents.

### Production caution

A valid bundle can still implement a bad business policy. Transactional activation protects consistency, not correctness.

---

## Q8. What is a policy control plane for OPA?

### Detailed answer

A policy control plane manages authoring, review, testing, packaging, signing, promotion, distribution, inventory, status, decision telemetry, and rollback across OPA instances. OPA provides APIs and plugins for bundles, discovery, status, and decision logs, but the open-source agent does not by itself provide a complete enterprise control-plane service.

A corporate design normally includes source control, CI, artifact storage, environment promotion, credentials, observability, and ownership metadata.

### Example / evidence

```text
Control-plane capabilities:
- Policy repository and CODEOWNERS
- Rego tests, lint, compatibility and benchmark gates
- Immutable signed bundles
- Staged rollout and rollback
- Agent status and revision inventory
- Decision-log pipeline with masking
- Audit and exception workflow
```

### L3 follow-up discussion

Discuss separation of duties between policy authors, application owners, security governance, and platform operators.

### Production caution

Do not distribute directly from an engineer's workstation to production OPA instances.

---

## Q9. How do you define a stable OPA decision contract?

### Detailed answer

Define the entrypoint, input schema, output type, defaults, error behavior, version, ownership, and compatibility policy. Prefer a structured object over an undocumented Boolean when callers need reasons, obligations, filters, or policy metadata.

The contract should distinguish deny from unavailable, invalid input, policy evaluation error, and stale-policy conditions. Use schema validation in the caller and policy tests for representative versions.

### Example / evidence

```text
Example:
{
  "version": "v1",
  "allow": false,
  "reasons": [{"code":"AUTHZ_004","message":"tenant mismatch"}],
  "obligations": [],
  "policy_revision": "2026-08-01.3"
}
```

### L3 follow-up discussion

Explain how policy revision may be obtained from server provenance or decision telemetry rather than hard-coded inside every rule.

### Production caution

Changing a Boolean decision into an object without coordinating callers can silently break enforcement.

---

## Q10. How do you decide fail-open versus fail-closed?

### Detailed answer

Fail-closed rejects an operation when a reliable decision cannot be obtained. It is appropriate for high-risk authorization, privileged changes, and compliance controls where unauthorized access is worse than unavailability. Fail-open preserves availability when policy infrastructure fails but accepts security or compliance exposure.

The choice should be made per operation and failure type, not globally. A health-check endpoint, read-only low-risk request, emergency response path, and financial transfer may need different behavior.

### Example / evidence

```text
Decision matrix fields:
- Operation risk
- Data sensitivity
- Business availability target
- Expected outage duration
- Compensating controls
- Auditability
- Break-glass path
- Error versus explicit deny
```

### L3 follow-up discussion

Discuss cached last-known-good decisions, local OPA deployment, bounded timeouts, and circuit breakers as ways to reduce the fail-open/fail-closed trade-off.

### Production caution

Do not treat policy evaluation errors as explicit allows. Record and alert on every fallback decision.

---

## Official references

- https://www.openpolicyagent.org/docs
- https://www.openpolicyagent.org/docs/management-introduction
- https://www.openpolicyagent.org/docs/integration
- https://www.openpolicyagent.org/docs/operations

---

<!-- Original file: 02. Rego v1 Fundamentals and Rule Semantics.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Rego v1 Fundamentals and Rule Semantics

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Rego v1 migration, rules, unification, variables, defaults, negation, functions, and types.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What changed with Rego v1 syntax?

### Detailed answer

Rego v1 made keywords such as `if`, `contains`, `in`, and `every` part of the language and requires clearer rule declarations. Rule bodies use `if`, and incremental multi-value rules use `contains`. Deprecated built-ins and ambiguous older syntax require migration review.

On OPA v1.x, `import rego.v1` is not required, although policy libraries that must also run on compatible pre-1.0 OPA versions may retain it according to their support strategy.

### Example / evidence

```text
package authz

default allow := false

allow if {
  input.subject.role == "admin"
}

reasons contains "missing owner label" if {
  not input.resource.metadata.labels.owner
}
```

### L3 follow-up discussion

Explain bundle manifest Rego-version metadata and why producers and consumers must agree during migration.

### Production caution

Do not mix untested v0 and v1 syntax across bundles. Compilation failure can block policy activation.

---

## Q2. Explain complete rules and multi-value rules.

### Detailed answer

A complete rule produces at most one value for a given evaluation. Examples include a Boolean `allow`, a numeric limit, or an object decision. Multiple rule bodies may contribute to a complete rule only when they produce a consistent value.

A multi-value rule incrementally builds a set or object. In Rego v1, `contains` clearly declares set-producing rules. Use comprehensions when a value is naturally derived in one expression.

### Example / evidence

```text
default allow := false
allow if input.role == "admin"

violations contains {"code":"P001","msg":"privileged container"} if {
  input.container.securityContext.privileged == true
}
```

### L3 follow-up discussion

Discuss complete-rule conflicts, where two matching rule bodies produce different values and evaluation returns an error.

### Production caution

A conflict is not a deny result. The integration must surface evaluation errors and policy tests must cover overlapping rule conditions.

---

## Q3. What is unification in Rego?

### Detailed answer

Unification uses `=` to make terms equal and can bind previously unbound variables. Assignment `:=` binds a variable once in the current scope and is generally clearer for local values. Equality comparison `==` compares already determined values.

Understanding the difference prevents accidental variable rebinding, ambiguous logic, and migration problems.

### Example / evidence

```text
some user in input.users
user.id = input.requested_id

name := user.name
name == input.expected_name
```

### L3 follow-up discussion

Explain why the style guide generally favors `:=` for assignment and `==` for comparison, reserving `=` for intentional unification.

### Production caution

Overusing unification can make policies difficult to review and can produce multiple solutions unexpectedly.

---

## Q4. How do variables become safe in Rego?

### Detailed answer

A variable is safe when OPA can determine its values from positive expressions before it is used in contexts such as negation, rule heads, or certain built-ins. Unsafe-variable compile errors commonly result from typos, missing data paths, or using a variable only inside `not`.

Bind variables through input/data iteration or assignment before using them.

### Example / evidence

```text
deny contains msg if {
  some container in input.spec.containers
  container.securityContext.privileged == true
  name := container.name
  msg := sprintf("container %q is privileged", [name])
}
```

### L3 follow-up discussion

Explain that fixing the first unsafe variable often resolves multiple cascading compiler errors.

### Production caution

Do not silence unsafe-variable errors by replacing variables with broad wildcards; verify the intended data relationship.

---

## Q5. How do `some`, `in`, and iteration work?

### Detailed answer

`some` declares local variables, while `in` iterates collection values or key/value pairs. Rego evaluation explores possible bindings declaratively. Policies should make collection type assumptions explicit and avoid repeated broad scans.

Use descriptive variables and small helper rules for readability.

### Example / evidence

```text
has_required_role if {
  some role in input.subject.roles
  role in {"admin", "security-reviewer"}
}

invalid_images contains image if {
  some container in input.review.object.spec.containers
  image := container.image
  not startswith(image, "registry.example.com/")
}
```

### L3 follow-up discussion

Discuss arrays versus sets: array order and duplicates are retained, while sets are unordered and unique.

### Production caution

Iteration over an undefined or wrong-type field can make a rule undefined. Validate input contracts or use defensive helper rules.

---

## Q6. What are defaults and why are they important?

### Detailed answer

A default gives a complete rule a value when no other rule body produces one. Authorization rules normally use `default allow := false` to make nonmatching requests deny. Defaults also stabilize object-shaped decisions.

A default does not catch compile errors or all evaluation errors; those remain failures.

### Example / evidence

```text
default decision := {
  "allow": false,
  "reasons": [{"code":"DEFAULT_DENY","message":"no allow rule matched"}]
}

decision := {"allow": true, "reasons": []} if {
  valid_subject
  permitted_action
}
```

### L3 follow-up discussion

Explain how multiple decision rule bodies must remain mutually consistent or be combined into one object-building design.

### Production caution

Do not rely on the caller to convert undefined into deny when the policy can provide an explicit default.

---

## Q7. How does negation work in Rego?

### Detailed answer

`not expression` succeeds when the expression cannot be proven true. This is negation-as-failure, not a three-valued Boolean negation. Variables used inside negation must already be safely bound, and missing data can make `not` succeed.

For security policies, distinguish truly false from absent fields through schema validation or explicit presence checks.

### Example / evidence

```text
missing_owner if {
  not input.metadata.labels.owner
}

# More explicit:
missing_owner if {
  object.get(input.metadata, "labels", {}) == {}
}
```

### L3 follow-up discussion

Discuss how negation interacts with partial data and why unexpected missing fields can turn into unintended allows.

### Production caution

Do not write allow rules whose critical security condition is merely the absence of a deny signal unless input completeness is guaranteed.

---

## Q8. How do functions work in Rego?

### Detailed answer

Functions accept arguments and produce a value. They improve reuse for normalization, classification, and calculations. Function inputs should be explicit, behavior deterministic, and errors controlled.

Multiple function definitions may exist, but overlapping definitions producing different outputs can conflict.

### Example / evidence

```text
is_corporate_email(email) if {
  is_string(email)
  endswith(lower(email), "@example.com")
}

risk_score(action) := 100 if action == "delete"
risk_score(action) := 10 if action == "read"
```

### L3 follow-up discussion

Discuss whether a helper should be a Boolean rule, value rule, function, or data table. Data-driven maps are often easier to review than many conditional branches.

### Production caution

Avoid hidden dependencies on global input inside reusable functions when explicit parameters make the contract clearer.

---

## Q9. What are sets, arrays, and objects in Rego?

### Detailed answer

Arrays are ordered collections and may contain duplicates. Sets are unordered unique collections and are useful for membership and policy violations. Objects map keys to values and are appropriate for decision contracts and reference data.

Use the collection whose semantics match the business requirement; converting repeatedly can add cost and confusion.

### Example / evidence

```text
allowed_roles := {"reader", "writer"}
ordered_steps := ["validate", "authorize", "execute"]
decision := {
  "allow": count(violations) == 0,
  "violations": sort([v | some v in violations])
}
```

### L3 follow-up discussion

Because JSON has no set type, sets are serialized as arrays and ordering should not be treated as stable unless explicitly sorted.

### Production caution

Do not use unordered set output directly for deterministic golden-file tests without normalization.

---

## Q10. How do built-in functions affect portability?

### Detailed answer

OPA provides built-ins for strings, arrays, objects, regex, time, JWT, HTTP, crypto, graph operations, and more. Availability can differ by OPA version and evaluation target such as Wasm. Capabilities files let tooling restrict policies to supported built-ins and language features.

A corporate policy repository should declare the minimum runtime and target capabilities.

### Example / evidence

```text
Validation gates:
`opa check --strict policy/`
`opa build --capabilities capabilities.json ...`
`regal lint policy/`

Policy metadata should state OPA and integration targets.
```

### L3 follow-up discussion

Discuss nondeterministic or environment-dependent built-ins and why policies should usually remain pure and locally evaluable.

### Production caution

A policy that compiles in the CLI may fail in a Wasm or embedded target if it uses unsupported built-ins.

---

## Official references

- https://www.openpolicyagent.org/docs/policy-language
- https://www.openpolicyagent.org/docs/v0-upgrade
- https://www.openpolicyagent.org/docs/style-guide

---

<!-- Original file: 03. Advanced Rego Data Modeling and Policy Design.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Advanced Rego, Data Modeling, and Policy Design

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Comprehensions, with, every, objects, indexing, schemas, metadata, packages, and deterministic decisions.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What are comprehensions and when should they be used?

### Detailed answer

Array, set, and object comprehensions derive a collection from expressions. They are useful for filtering, mapping, and aggregating input or data in one declarative construct.

Comprehensions can be expensive when repeatedly evaluated over large collections. Bind narrow keys early and consider helper rules or indexed data.

### Example / evidence

```text
privileged_names := [
  c.name |
  some c in input.spec.containers
  c.securityContext.privileged == true
]

role_set := {r | some r in input.subject.roles}
```

### L3 follow-up discussion

Explain the difference between a comprehension value and an incremental rule that can be extended across modules.

### Production caution

Nested comprehensions over large input can create quadratic work. Benchmark representative documents.

---

## Q2. How does the `with` keyword support testing and policy reuse?

### Detailed answer

`with` temporarily replaces `input`, `data`, or a referenced function for one expression. It is heavily used in tests to inject inputs, base data, and mock built-ins or helper functions.

Replacement scope is limited to the expression and nested evaluation. Overuse can make tests difficult to read, so fixtures and helper functions should remain clear.

### Example / evidence

```text
test_admin_allowed if {
  data.authz.allow
    with input as {"subject":{"role":"admin"}}
}

test_lookup_failure if {
  result := data.app.decision
    with data.directory.users as {}
  not result.allow
}
```

### L3 follow-up discussion

Discuss limitations around mocking and how test doubles should preserve the real function's return type and failure behavior.

### Production caution

Tests that mock every dependency cannot prove integration with actual bundle data or external services.

---

## Q3. What does `every` do?

### Detailed answer

`every` asserts that a condition holds for every element in a collection. It is clearer than patterns that search for a counterexample and negate it. For an empty collection, universal conditions commonly evaluate true, so minimum-count requirements must be separate.

Use `every` for rules such as all images are from approved registries or every container has limits.

### Example / evidence

```text
all_images_approved if {
  count(input.spec.containers) > 0
  every container in input.spec.containers {
    startswith(container.image, "registry.example.com/")
  }
}
```

### L3 follow-up discussion

Explain vacuous truth and why an empty container list might satisfy `every` unless explicitly rejected.

### Production caution

Do not assume `every` proves the collection field exists or has the expected type.

---

## Q4. How can object operations simplify defensive policy?

### Detailed answer

Built-ins such as `object.get`, `object.keys`, `object.union`, and `object.filter` can handle optional fields and create normalized views. `object.get` is especially useful for defaults when a field is missing.

Defensive access should not hide malformed input that should be rejected by schema validation.

### Example / evidence

```text
labels := object.get(input.metadata, "labels", {})
owner := object.get(labels, "owner", "")

missing_owner if owner ==
```

### L3 follow-up discussion

Discuss normalizing the enforcement-point input once rather than scattering missing-field logic across every rule.

### Production caution

A default value can collapse “field absent” and “field explicitly empty”; preserve the distinction when policy requires it.

---

## Q5. How do rule indexing and early binding improve performance?

### Detailed answer

OPA can index rules using equality expressions and known values so it evaluates only relevant branches. Policies perform better when selective conditions bind input fields early and avoid broad iteration before filtering.

Data structures keyed by tenant, action, resource type, or ID can replace repeated scans of arrays.

### Example / evidence

```text
Prefer:
permission := data.permissions[input.subject.role][input.action]

Instead of:
some p in data.permission_list
p.role == input.subject.role
p.action == input.action
```

### L3 follow-up discussion

Use profiling rather than assuming syntactic changes improve performance. Indexing behavior depends on rule structure and query.

### Production caution

Duplicating large reference data into several indexes increases bundle size and memory; measure the trade-off.

---

## Q6. How do annotations and metadata help policy governance?

### Detailed answer

Rego annotations can document packages, rules, entrypoints, scopes, authorship, and related metadata. Tooling can use annotations for documentation, schema association, and entrypoint discovery.

Enterprise repositories should combine annotations with CODEOWNERS, policy IDs, severity, business owner, and exception links.

### Example / evidence

```text
# METADATA
# title: Authorize account operations
# description: Enforces tenant ownership and role permissions
# entrypoint: true
package account.authz
```

### L3 follow-up discussion

Explain how metadata can drive generated documentation and policy catalogs while remaining subject to review and validation.

### Production caution

Metadata does not enforce ownership or severity by itself; CI and operational systems must consume it.

---

## Q7. What are schemas and type checking in OPA tooling?

### Detailed answer

OPA tooling can use JSON schemas to improve static checking of input and data references. Schemas detect misspelled fields, incorrect types, and contract drift earlier than runtime tests alone.

Schema coverage should include versioned input documents and important base-data structures.

### Example / evidence

```text
Typical workflow:
- Maintain `schemas/input-v1.json`.
- Associate schema through annotations or tool configuration.
- Run `opa check --strict`.
- Test older and newer contract versions.
- Reject undocumented fields where appropriate.
```

### L3 follow-up discussion

Discuss open versus closed schemas and compatibility when producers add fields or change nullability.

### Production caution

A schema cannot prove business correctness. Tests must still cover authorization combinations and negative cases.

---

## Q8. How do package and import design affect maintainability?

### Detailed answer

Use packages that align with policy domains and stable entrypoints, not deployment-team names or individual files. Imports should make dependencies explicit without creating deep cross-package coupling.

Keep reusable helpers pure and domain-neutral where practical. Avoid circular conceptual dependencies and wildcard-style shared global data.

### Example / evidence

```text
Example structure:
policy/
  authz/http/
  kubernetes/workload/
  terraform/network/
  lib/strings/
  lib/identity/
data/
  roles.json
  environments.json
```

### L3 follow-up discussion

Discuss semantic versioning of shared policy libraries and how a bundle can package modules atomically.

### Production caution

A giant `common` package becomes an ungoverned dependency and makes changes difficult to assess.

---

## Q9. How can policy return human-readable and machine-readable violations?

### Detailed answer

Return structured violations containing a stable code, severity, message, resource path, remediation, and optional metadata. Humans read messages; automation keys on stable codes and paths.

Messages should be deterministic, concise, and free of secrets. Sorting may be required before output.

### Example / evidence

```text
violations contains {
  "code": "K8S_SEC_001",
  "severity": "high",
  "message": sprintf("container %q must not be privileged", [c.name]),
  "field": "spec.containers.securityContext.privileged"
} if {
  some c in input.spec.containers
  c.securityContext.privileged == true
}
```

### L3 follow-up discussion

Explain localization and why policy code should not make message text the only stable interface.

### Production caution

Changing message wording should not break CI consumers or exception matching.

---

## Q10. How do you prevent policy ambiguity and overlap?

### Detailed answer

Define mutually exclusive rule conditions, explicit priorities in data, or one aggregation rule that resolves candidates deterministically. Use tests for overlapping roles, multiple tenants, wildcard permissions, and contradictory configuration.

OPA does not provide an implicit “first rule wins” model. Declarative rules can all contribute, and complete-rule conflicts are errors.

### Example / evidence

```text
Pattern:
candidates contains decision if { ... }

final_decision := choose(candidates)

Tests:
- zero candidates
- one candidate
- equivalent candidates
- conflicting candidates
```

### L3 follow-up discussion

Discuss deny-overrides, allow-overrides, first-applicable, and permit-unless-denied combining algorithms as explicit business choices.

### Production caution

Do not rely on source-file order to resolve policy conflicts.

---

## Official references

- https://www.openpolicyagent.org/docs/policy-language
- https://www.openpolicyagent.org/docs/style-guide
- https://www.openpolicyagent.org/docs/policy-performance

---

<!-- Original file: 04. OPA Policy Testing Linting and CI Quality.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Testing, Linting, and CI Quality

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Unit tests, matrices, mocking, coverage, Regal, compatibility, golden tests, fuzzing, and policy pipelines.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How do you test Rego policies with `opa test`?

### Detailed answer

OPA discovers rules prefixed with `test_` in supplied Rego files. Tests evaluate policy with controlled input and data and pass when the test rule is true. Use separate `_test.rego` files, descriptive names, positive and negative cases, and fixtures that represent real enforcement input.

Run recursively in CI and use `--fail-on-empty` so a path or naming error cannot produce a false green build.

### Example / evidence

```text
opa test --fail-on-empty --verbose ./policy ./testdata
opa test --run 'test_http_authz_' ./policy
opa test --format=json ./policy > test-results.json
```

### L3 follow-up discussion

Explain FAIL versus ERROR versus SKIPPED and why runtime errors are not equivalent to expected deny decisions.

### Production caution

A test suite with only allowed cases can hide fail-open behavior and missing default rules.

---

## Q2. What should an authorization policy test matrix contain?

### Detailed answer

Cover subject identity, tenant, role, action, resource type, ownership, environment, authentication strength, missing fields, malformed types, default deny, explicit deny, conflicting grants, and error paths. Include boundary and combinatorial cases.

Risk-based pairwise testing can reduce volume, but high-risk administrative actions deserve exhaustive negative tests.

### Example / evidence

```text
Minimum cases:
- authorized role and owner
- correct role, wrong tenant
- owner, wrong action
- unknown role
- missing subject
- empty permission data
- conflicting rule branches
- unsupported input version
```

### L3 follow-up discussion

Use data-driven test helpers to make the matrix readable while preserving a clear assertion per case.

### Production caution

Do not encode the same flawed permission table independently in both policy and tests; use test cases derived from business requirements.

---

## Q3. How is `with` used for test mocking?

### Detailed answer

`with` replaces input, data, or selected functions for the expression being tested. It allows unit tests to isolate a package from directory data, time, network helpers, or other packages.

Mocks must return the same type and representative errors as the real dependency. Maintain integration tests using real bundle data so unit mocks do not become the only evidence.

### Example / evidence

```text
test_expired_token_denied if {
  not data.authz.allow
    with input as token_request
    with time.now_ns as 1893456000000000000
}

test_empty_directory_denied if {
  not data.authz.allow
    with data.directory.users as {}
}
```

### L3 follow-up discussion

Discuss why time and nondeterministic built-ins should be injected or replaced to make tests repeatable.

### Production caution

Do not mock the final decision rule itself; test the actual policy behavior.

---

## Q4. How do you measure and enforce coverage?

### Detailed answer

`opa test --coverage` reports evaluated and unevaluated policy lines. Coverage identifies untested branches and rules, but it does not measure requirement quality, assertion strength, or security completeness.

Set a CI threshold appropriate to the repository and require reviewers to inspect high-risk uncovered code rather than chasing a percentage alone.

### Example / evidence

```text
opa test --coverage --format=json ./policy > coverage.json
opa test --coverage --threshold 90 ./policy
```

### L3 follow-up discussion

Rule indexing can affect which lines execute, so interpret uncovered expressions in the context of possible input paths.

### Production caution

One hundred percent line coverage can still miss authorization combinations and incorrect expected results.

---

## Q5. What is Regal, and how does it complement OPA tooling?

### Detailed answer

Regal is a Rego linter and language-server ecosystem tool that checks style, idioms, imports, bugs, and maintainability patterns. It complements `opa check`, `opa fmt`, and `opa test`.

Corporate CI should pin the Regal version and configuration, classify mandatory versus advisory rules, and document narrow suppressions.

### Example / evidence

```text
opa fmt --fail ./policy
opa check --strict ./policy
regal lint ./policy
opa test --fail-on-empty ./policy
```

### L3 follow-up discussion

Explain why compiler success, lint success, tests, and performance tests are separate quality dimensions.

### Production caution

Do not disable broad lint categories merely because legacy policy has many findings; baseline and remediate them deliberately.

---

## Q6. How do you test policy compatibility across OPA versions?

### Detailed answer

Run compilation and tests against every supported runtime or use capabilities files that represent target versions. Test bundle build and activation, not only source evaluation. During Rego v0-to-v1 migration, include producer and consumer combinations and bundle manifest metadata.

A compatibility matrix should cover OPA server, embedded SDK, Wasm target, and Gatekeeper's embedded OPA where used.

### Example / evidence

```text
CI matrix:
- opa 1.17.x
- opa 1.18.x
- opa 1.19.x
- Gatekeeper-supported embedded OPA
- Wasm build for declared entrypoints
```

### L3 follow-up discussion

Discuss minimum supported versus tested-latest policies and how emergency security upgrades are introduced.

### Production caution

Do not assume a new OPA binary is backward-compatible with every old policy bundle without checking migration notes and capabilities.

---

## Q7. How do you create golden tests for structured decisions?

### Detailed answer

Golden tests compare normalized decision output with an approved file. They are useful for complex violations, filters, and obligation objects. Normalize unordered sets, timestamps, generated IDs, and policy revision fields before comparison.

Review golden-file changes as business-policy changes, not routine test updates.

### Example / evidence

```text
opa eval --format=json   --data policy   --input testdata/request.json   'data.company.authz.decision' |
jq -S '.result[0].expressions[0].value | .violations |= sort_by(.code,.field)'   > actual.json

diff -u expected.json actual.json
```

### L3 follow-up discussion

Use focused assertions for critical behavior in addition to full golden output so reviewers can see the security intent.

### Production caution

Blindly accepting regenerated golden files can approve a mass policy regression.

---

## Q8. How should property and fuzz testing be applied to policy?

### Detailed answer

Generate inputs that test invariants such as “unknown roles never gain access,” “cross-tenant access is always denied,” “adding a deny flag cannot increase permission,” or “malformed input never returns allow.” Fuzzing is especially valuable for nested optional fields and type variation.

Use a harness in Go, Python, or another testing tool to generate JSON and call OPA or its SDK.

### Example / evidence

```text
Example invariants:
- `allow` implies authenticated subject ID exists.
- `allow` implies requested tenant equals subject tenant.
- Every denial contains at least one stable reason code.
- Evaluation never conflicts for schema-valid input.
```

### L3 follow-up discussion

Seed and retain every failing generated case as a deterministic regression test.

### Production caution

Random testing without explicit invariants produces noise rather than security evidence.

---

## Q9. How do you benchmark policy changes in CI?

### Detailed answer

Use representative inputs and `opa eval --metrics --profile`, `opa bench`, or `opa test --bench`. Track latency percentiles, allocations, evaluation count, and bundle size. Establish budgets per entrypoint and compare against a stable runner baseline.

Benchmark large and worst-case documents, not only a minimal happy-path request.

### Example / evidence

```text
opa eval --data bundle.tar.gz   --input testdata/large-request.json   --metrics --profile   'data.company.authz.decision'

opa test --bench ./policy
```

### L3 follow-up discussion

Discuss benchmark noise, CPU throttling, warm-up, caching, and why relative regressions are often more useful than absolute microseconds in shared CI.

### Production caution

Do not optimize by weakening the policy or removing required diagnostic output without business approval.

---

## Q10. What should a policy CI/CD pipeline contain?

### Detailed answer

A mature pipeline formats, compiles strictly, lints, validates schemas, runs unit and integration tests, checks coverage, benchmarks critical entrypoints, scans dependencies and artifacts, builds an immutable bundle, signs or verifies provenance, promotes through environments, and records revision metadata.

Production publication should require review by the policy owner and affected service owners for contract changes.

### Example / evidence

```text
Stages:
1. `opa fmt --fail`
2. `opa check --strict`
3. `regal lint`
4. `opa test --fail-on-empty --coverage`
5. compatibility and benchmark matrix
6. `opa build`
7. checksum/sign/provenance
8. staging activation and canary decisions
9. production promotion
```

### L3 follow-up discussion

Explain policy rollback as artifact rollback and why data compatibility must be considered alongside Rego modules.

### Production caution

Do not build a different bundle for production after testing; promote the same immutable artifact.

---

## Official references

- https://www.openpolicyagent.org/docs/policy-testing
- https://www.openpolicyagent.org/docs/style-guide
- https://www.openpolicyagent.org/docs/policy-performance
- https://www.openpolicyagent.org/docs/cli

---

<!-- Original file: 05. OPA Integration REST API SDK and Wasm.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Integration, REST API, SDK, and Wasm

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Daemon, sidecar, SDK, Data/Query/Compile APIs, Wasm, input contracts, and failure handling.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What OPA integration models are available?

### Detailed answer

Common models include an OPA daemon accessed over HTTP, a sidecar next to an application, a node-local or host daemon, the Go SDK embedded in a service, Wasm compiled policy embedded in another runtime, Envoy external authorization, and specialized integrations such as Gatekeeper.

Choose based on latency, language/runtime, failure isolation, update lifecycle, supported built-ins, memory, observability, and operational ownership.

### Example / evidence

```text
Decision questions:
- Is a network hop acceptable?
- Must policy update independently from the app?
- Which language/runtime hosts enforcement?
- Are all built-ins supported?
- Who owns agent upgrades and bundle credentials?
- What is the unavailable-policy behavior?
```

### L3 follow-up discussion

Explain why embedded policy reduces network dependency but couples process memory, release cadence, and crash domain to the application.

### Production caution

Do not choose an integration solely from benchmark latency; lifecycle and failure semantics usually dominate.

---

## Q2. How does the OPA Data API work?

### Detailed answer

The Data API evaluates a document path under `data`, optionally using a JSON input document. It returns a result when defined and can expose metrics, explanation, provenance, or other evaluation options depending on the endpoint and query.

The caller must distinguish HTTP errors, JSON parsing errors, evaluation errors, and an undefined decision with no result.

### Example / evidence

```text
POST /v1/data/company/authz/decision
Content-Type: application/json

{"input":{"subject":{"id":"u1"},"action":"read","resource":{"id":"r1"}}}
```

### L3 follow-up discussion

Use a typed client wrapper that validates response shape and centralizes timeout, correlation, and fail behavior.

### Production caution

An HTTP 200 response does not necessarily mean “allow”; inspect and validate the decision document.

---

## Q3. When should the Query API be used?

### Detailed answer

The Query API evaluates an arbitrary Rego query. It is useful for debugging, tooling, exploration, and administrative workflows. Production enforcement usually prefers a fixed Data API entrypoint to limit query surface and stabilize the contract.

Allowing callers to submit arbitrary queries increases security, performance, and governance risk.

### Example / evidence

```text
Fixed production query:
`data.company.authz.decision`

Administrative query:
`data.system.main`
or carefully controlled diagnostics.
```

### L3 follow-up discussion

Discuss OPA API authorization policies that restrict which API paths and query capabilities a client may access.

### Production caution

Do not expose arbitrary query or policy-write APIs to untrusted application clients.

---

## Q4. What is the Compile API and partial evaluation?

### Detailed answer

The Compile API partially evaluates a query using known policy and data and returns residual conditions for unknown input. It is useful for translating policy into filters, pushing authorization closer to a database, or preparing specialized evaluation.

The caller must correctly interpret support modules, unknowns, and residual expressions. A simplified result is not automatically SQL or another target language.

### Example / evidence

```text
Example use:
Known: subject identity and tenant policy.
Unknown: individual resource attributes.
Output: residual conditions that a data layer can translate into a safe filter.
```

### L3 follow-up discussion

Discuss query planner correctness, unsupported expressions, and fallback to full evaluation when translation cannot preserve semantics.

### Production caution

An incomplete translator can under-filter data and become an authorization vulnerability.

---

## Q5. How do you integrate OPA through the Go SDK?

### Detailed answer

The Go SDK can load bundles and evaluate decisions in-process while managing plugins and configuration. It avoids a network hop and returns native Go values, but the application now shares resources and lifecycle with OPA.

The application must set decision paths, input types, context deadlines, readiness checks, and structured error handling.

### Example / evidence

```text
Operational concerns:
- OPA SDK and app version compatibility
- bundle activation status
- memory and CPU budgets
- application startup readiness
- policy rollback
- decision metrics and logs
```

### L3 follow-up discussion

Explain when the lower-level Rego package is appropriate versus the higher-level SDK with bundle and plugin management.

### Production caution

A policy panic-like resource exhaustion or huge bundle can affect the application process when embedded.

---

## Q6. What are the trade-offs of Wasm policy evaluation?

### Detailed answer

Wasm allows compiled policy to run in supported non-Go environments and avoids a separate OPA daemon. It can provide low-latency local decisions and portable deployment, but not every built-in or dynamic OPA feature is available. Policy and entrypoints are compiled ahead of time.

The host must implement the Wasm ABI, data loading, memory management, error handling, and artifact update lifecycle, or use a maintained SDK.

### Example / evidence

```text
Build:
`opa build -t wasm -e company/authz/decision policy/ data/`

Validate:
- supported built-ins
- entrypoint
- artifact checksum
- host SDK compatibility
- performance and memory
```

### L3 follow-up discussion

Discuss how policy updates require distributing a new Wasm artifact and possibly data, unlike a daemon fetching bundles independently.

### Production caution

Do not assume a Rego policy that runs in the OPA CLI can compile and behave identically under Wasm without compatibility tests.

---

## Q7. How should input be constructed by an application?

### Detailed answer

Create a canonical, versioned input document from trusted authentication context, normalized request attributes, resource metadata, and environmental facts. Remove ambiguous duplicate sources and document null, absent, and empty behavior.

The enforcement point should validate its own input schema before calling OPA and should not pass entire unbounded request objects unnecessarily.

### Example / evidence

```text
Recommended top-level structure:
{
  "version":"v1",
  "subject": {...},
  "action":"...",
  "resource": {...},
  "context": {"request_id":"...","time":"..."}
}
```

### L3 follow-up discussion

Discuss privacy and data minimization: OPA only needs attributes required by policy, and decision logs may capture input.

### Production caution

Do not forward credentials, full tokens, personal payloads, or secrets that policy does not require.

---

## Q8. How do you secure an OPA HTTP integration?

### Detailed answer

Use TLS, preferably mutual authentication or a protected local channel, and OPA API authentication and authorization. Restrict network exposure, run with least privilege, limit accessible API paths, protect bundle and log credentials, and set resource limits and timeouts.

Application clients normally need only the decision path, not policy/data mutation or diagnostic APIs.

### Example / evidence

```text
Controls:
- loopback or sidecar-only listener where possible
- TLS certificate and key
- client identity
- `system.authz` policy
- network policy/firewall
- request size and timeouts
- audit/decision correlation
```

### L3 follow-up discussion

Explain why `/health` and `/metrics` access may need separate monitoring identity and policy rules.

### Production caution

OPA APIs have no safe implicit trust boundary when exposed without authentication and authorization.

---

## Q9. How do you handle OPA unavailability in an application?

### Detailed answer

Use a bounded timeout, distinguish transport failure from explicit deny, apply the approved fail-open/fail-closed matrix, emit a high-signal metric and audit event, and avoid retry storms. Local deployment, circuit breakers, and last-known-good policy reduce dependency risk.

The response to unavailable policy should be tested like any other security control.

### Example / evidence

```text
Failure states:
- connection refused
- deadline exceeded
- TLS/auth failure
- HTTP 5xx
- invalid response
- undefined result
- evaluation error
- stale policy
```

### L3 follow-up discussion

Discuss whether cached decisions are safe given subject, resource, action, policy revision, and revocation requirements.

### Production caution

Never silently convert all OPA errors into allow to protect application availability.

---

## Q10. How do you version an OPA integration contract?

### Detailed answer

Version input and output schemas independently from OPA binary versions. Support overlapping versions during rolling upgrades, use adapters at the enforcement point, and test old caller/new policy and new caller/old policy combinations.

Breaking fields or decision types require coordinated deployment and explicit deprecation.

### Example / evidence

```text
Contract metadata:
- `input.version`
- entrypoint path
- output schema version
- minimum OPA capabilities
- bundle revision
- deprecation deadline
- owning teams
```

### L3 follow-up discussion

A policy can expose `decision_v1` and `decision_v2` entrypoints during migration while callers move gradually.

### Production caution

Do not infer business contract compatibility only from semantic version of the OPA executable.

---

## Official references

- https://www.openpolicyagent.org/docs/integration
- https://www.openpolicyagent.org/docs/rest-api
- https://www.openpolicyagent.org/docs/wasm
- https://www.openpolicyagent.org/docs/security

---

<!-- Original file: 06. Bundles Discovery Status and Decision Logs.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Bundles, Discovery, Status, and Decision Logs

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Policy distribution, roots, revisions, discovery, telemetry, masking, rollout, and control-plane design.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What are OPA bundles?

### Detailed answer

Bundles package Rego modules and data for distribution to OPA. They provide an asynchronous, versioned way to update policy and reference data without callers pushing individual documents. OPA downloads, verifies, parses, compiles, and activates a bundle as a coherent snapshot.

Bundles support centralized policy management with distributed local evaluation.

### Example / evidence

```text
Typical bundle:
bundle.tar.gz
  /authz/policy.rego
  /data.json
  /.manifest

Build:
`opa build -b policy-directory -o bundle.tar.gz`
```

### L3 follow-up discussion

Discuss bundle roots, names, revisions, ETags, persistence, signing, and compatibility with multiple agents.

### Production caution

Do not publish mutable content under an untracked URL without revision and integrity controls.

---

## Q2. What are bundle roots, and why do they matter?

### Detailed answer

Bundle roots declare which sections of the `data` namespace the bundle owns. They prevent incompatible bundles from writing overlapping paths and allow multiple bundles to manage separate policy domains.

Roots must match package and data design and be reviewed when repositories are split or merged.

### Example / evidence

```text
Examples:
- `["company/authz"]`
- `["kubernetes"]`
- empty root for a bundle that owns the entire data tree

Overlapping roots can cause activation errors.
```

### L3 follow-up discussion

Use ownership boundaries that reflect organizational responsibility and avoid one bundle accidentally replacing another domain.

### Production caution

Changing roots is a data-ownership migration and can remove documents from the active store if not planned.

---

## Q3. How do bundle revisions support operations?

### Detailed answer

A bundle revision is metadata identifying the policy/data artifact. Status reports and decision logs can associate evaluations with the active revision, enabling incident timelines, fleet convergence checks, and rollback.

Use immutable revisions tied to source commit and build provenance.

### Example / evidence

```text
Example revision:
`git:9f42.../build:20260801.3`

Track:
- published time
- environment
- checksum/signature
- test evidence
- active agent count
- rollback target
```

### L3 follow-up discussion

Revision should identify content, not just a friendly release name that may be overwritten.

### Production caution

If decision logs omit usable revision metadata, proving which policy authorized an incident becomes difficult.

---

## Q4. What is delta bundle support?

### Detailed answer

Delta bundles describe incremental changes rather than sending a complete snapshot, reducing transfer cost for large datasets. They increase control-plane complexity because the agent applies patches relative to existing state and update ordering matters.

Use them only when full bundle size or frequency justifies the operational complexity.

### Example / evidence

```text
Design checks:
- base revision identity
- patch ordering
- missed update recovery
- fallback full snapshot
- transaction and conflict behavior
- monitoring for divergence
```

### L3 follow-up discussion

Full snapshots remain valuable for bootstrap and reconciliation after a gap or corrupted local state.

### Production caution

Do not introduce delta distribution without a tested recovery path to a known full bundle.

---

## Q5. What is OPA discovery?

### Detailed answer

Discovery lets an OPA instance retrieve dynamic configuration, including services and plugin settings, from a discovery bundle. It supports centralized changes to agent configuration without rebuilding every deployment.

Because discovery controls where agents fetch policy and send telemetry, it is a highly privileged supply-chain component.

### Example / evidence

```text
Discovery can configure:
- bundle services
- decision logs
- status reporting
- plugin settings
- credentials references and polling behavior
```

### L3 follow-up discussion

Use a minimal bootstrap configuration that securely reaches discovery, then version and stage discovery changes like code.

### Production caution

A compromised discovery source can redirect policy, status, or decision logs across the fleet.

---

## Q6. What does the Status API/plugin provide?

### Detailed answer

OPA can report agent and plugin status to a remote service, including bundle download and activation state. A control plane uses status to detect stale agents, compile failures, unreachable services, and fleet rollout progress.

Status is telemetry, not a direct health guarantee for every application decision.

### Example / evidence

```text
Monitor:
- agent ID/version
- active bundle revision
- last successful activation
- error code/message
- discovery revision
- report freshness
```

### L3 follow-up discussion

Define a maximum acceptable reporting age and alert when agents disappear or remain on old revisions.

### Production caution

A green status endpoint does not prove the enforcement point is actually calling OPA or honoring decisions.

---

## Q7. What are decision logs?

### Detailed answer

Decision logs record policy query events and can include decision ID, path/query, input, result, bundle metadata, metrics, and labels depending on configuration. They support audit, debugging, policy usage analysis, and incident reconstruction.

Because input and result may contain sensitive data, masking, encryption, access control, retention, and volume planning are essential.

### Example / evidence

```text
Decision-log pipeline:
OPA -> buffered plugin -> authenticated HTTPS service -> protected storage -> searchable audit/analytics

Fields to retain:
timestamp, decision_id, path, result summary, revision, caller, latency
```

### L3 follow-up discussion

Discuss batching, upload retries, backpressure, dropped telemetry, and how logging must not block critical decisions indefinitely.

### Production caution

Do not enable full unmasked decision input globally without a data-classification review.

---

## Q8. How does decision log masking work?

### Detailed answer

Masking rules remove or replace selected fields before decision events leave OPA. Use masking for tokens, credentials, personal data, payload bodies, and unnecessary resource content.

Mask rules themselves must be tested against evolving input schemas; a newly added sensitive field may otherwise leak.

### Example / evidence

```text
Test examples:
- authorization header removed
- JWT/token field removed
- personal attributes reduced
- decision reason retained
- malformed input still masked safely
```

### L3 follow-up discussion

Prefer data minimization at input construction in addition to masking. Data never sent to OPA cannot leak through logs.

### Production caution

Masking is not encryption and does not protect data already present in local debug logs or traces.

---

## Q9. How should bundle rollout and rollback work?

### Detailed answer

Build one immutable artifact, validate it in lower environments, canary a small set of agents or traffic, monitor status and decision metrics, then expand in waves. Rollback points agents to the previous known-good artifact and verifies fleet convergence.

Policy and data must be rolled back together when their schemas or semantics are coupled.

### Example / evidence

```text
Release gates:
- compile/test/lint pass
- benchmark within budget
- signature verified
- canary deny/allow comparison
- no evaluation errors
- status target revision reached
- documented rollback revision
```

### L3 follow-up discussion

Shadow evaluation can compare new and old decisions before enforcement, but privacy and cost must be managed.

### Production caution

Do not delete previous bundle artifacts immediately after promotion.

---

## Q10. How do you design an OPA control-plane service?

### Detailed answer

Provide authenticated artifact publication, immutable storage, environment promotion, agent identity, secure configuration, bundle/discovery serving, decision-log ingestion, status inventory, policy catalog, exception workflow, and audit evidence.

Scale the control plane separately from decision evaluation. Agents should continue using last-known-good policy during temporary management-plane outages.

### Example / evidence

```text
Reliability design:
- redundant artifact endpoints
- cache-control/ETag
- signing and checksum verification
- multi-region distribution
- backpressure for logs
- agent staleness SLO
- disaster recovery for policy artifacts
```

### L3 follow-up discussion

Discuss build-versus-buy and how open-source OPA deliberately leaves the control-plane service as an integration responsibility.

### Production caution

The control plane is part of the security boundary and supply chain; treat it as Tier-1 infrastructure.

---

## Official references

- https://www.openpolicyagent.org/docs/management-introduction
- https://www.openpolicyagent.org/docs/management-bundles
- https://www.openpolicyagent.org/docs/management-decision-logs
- https://www.openpolicyagent.org/docs/management-status
- https://www.openpolicyagent.org/docs/management-discovery

---

<!-- Original file: 07. OPA Security Hardening and Policy Supply Chain.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Security Hardening and Policy Supply Chain

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Server security, API authorization, TLS, credentials, bundles, HTTP calls, data protection, exceptions, and upgrades.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How do you secure OPA running as a server?

### Detailed answer

Bind only to required interfaces, use TLS, client authentication where possible, OPA API authorization, network controls, least-privilege runtime identity, read-only filesystem where practical, protected credentials, resource limits, and patched images. Restrict policy/data write APIs and diagnostics to administrators.

Use separate identities and permissions for decision callers, monitoring, bundle download, decision-log upload, and status reporting.

### Example / evidence

```text
Security checklist:
- TLS certificate/private key
- authentication mode
- `system.authz` policy
- firewall/NetworkPolicy
- non-root UID
- immutable image digest
- read-only root filesystem
- secret mounts
- CPU/memory limits
- audit and decision logs
```

### L3 follow-up discussion

Explain bootstrapping: the authorization policy and credentials that protect OPA must be available safely at startup.

### Production caution

An OPA listener without authentication and authorization should not be exposed to untrusted networks.

---

## Q2. How does OPA API authorization work?

### Detailed answer

OPA can authorize requests to its own HTTP API using a policy under the system authorization path. The policy receives information about the API request and authenticated identity and decides whether access is allowed.

This allows separate permissions for evaluating approved decision paths, reading health/metrics, updating policies, querying arbitrary data, or using administrative APIs.

### Example / evidence

```text
Policy concepts:
- allow application identity to POST one Data API path
- allow monitoring identity to GET `/metrics` and `/health`
- allow control-plane identity to deliver bundles
- deny arbitrary Query and Policy APIs
```

### L3 follow-up discussion

Test the self-authorization policy carefully because an incorrect rule can lock out management or expose policy data.

### Production caution

Do not grant application service accounts access to policy mutation or arbitrary query endpoints.

---

## Q3. What TLS choices are important?

### Detailed answer

Use certificates with correct SANs, strong private-key protection, approved protocol minimums, automated rotation, and client trust verification. Mutual TLS provides client identity before policy authorization. OPA can periodically refresh certificate files depending on configuration.

The deployment must define behavior during rotation, expired certificates, missing intermediates, and trust-store updates.

### Example / evidence

```text
Validate:
- server name and SAN
- full certificate chain
- private key match and permissions
- client CA
- rotation without process outage
- monitoring identity access
```

### L3 follow-up discussion

TLS authentication may block unauthenticated health probes, requiring a protected monitoring path or infrastructure-aware probe design.

### Production caution

Do not bypass certificate verification in callers to solve trust-chain problems.

---

## Q4. How do you protect bundle and telemetry credentials?

### Detailed answer

Use separate least-privilege credentials for download and upload directions, short-lived workload identity where supported, secret mounts rather than command-line flags, controlled environment exposure, and audited rotation.

A bundle reader should not be able to overwrite bundles; a decision-log writer should not be able to read all stored logs.

### Example / evidence

```text
Credential classes:
- bundle/discovery read
- decision-log append
- status report write
- artifact publisher
- administrative OPA API client
```

### L3 follow-up discussion

Test expiry and rotation while agents continue using last-known-good policy and resume telemetry correctly.

### Production caution

Do not reuse one cloud access key across the entire OPA fleet and control plane.

---

## Q5. How do you secure policy supply chain?

### Detailed answer

Require reviewed source changes, protected branches, signed or attested builds, pinned tools, immutable bundle storage, integrity verification, controlled promotion, and agent status. Record source commit, builder, tests, dependencies, digest, and approvers.

Policy is executable security logic; compromise can grant access as effectively as application-code compromise.

### Example / evidence

```text
Supply-chain evidence:
- CODEOWNERS approval
- CI identity
- OPA/Regal versions
- test and coverage reports
- bundle SHA-256/signature
- SBOM for runtime image
- promotion record
```

### L3 follow-up discussion

Consider separation of duties between authors and production publishers, especially for authorization policy.

### Production caution

Do not let a production agent fetch policy from an unprotected development branch or mutable object.

---

## Q6. What are risks of `http.send` in policy?

### Detailed answer

`http.send` makes evaluation depend on an external service, adding latency, availability, TLS, authentication, data-leakage, and nondeterminism risks. It can also amplify load because every decision may trigger a request unless caching and architecture are designed.

Prefer supplying trusted data through input or bundles, or use an integration-specific external-data mechanism when necessary.

### Example / evidence

```text
Before use, define:
- strict timeout
- TLS and credentials
- allowed destinations
- cache semantics
- failure behavior
- response schema
- PII exposure
- rate limits and circuit breaker
```

### L3 follow-up discussion

Discuss strict built-in errors and how an HTTP error may halt evaluation rather than produce a normal deny.

### Production caution

Do not call unrestricted URLs derived from user input; this can create SSRF and data-exfiltration risk.

---

## Q7. How do you prevent sensitive data leakage through policy?

### Detailed answer

Minimize input, avoid returning unnecessary attributes, mask decision logs, restrict debugging and profiling, protect bundles containing reference data, and review error messages. Treat print output, traces, metrics labels, and test fixtures as possible disclosure paths.

Classify data before including it in `data` because every authorized query to related documents may reveal it.

### Example / evidence

```text
Sensitive paths:
- tokens and headers
- personal identifiers
- payment or medical attributes
- secrets in IaC plans
- Kubernetes Secrets
- internal topology
- private policy exceptions
```

### L3 follow-up discussion

Use synthetic or redacted test data and separate high-sensitivity policy domains when access requirements differ.

### Production caution

OPA is not a secret manager. Avoid using its in-memory data store as a general secrets database.

---

## Q8. How should policy exceptions be governed?

### Detailed answer

Exceptions must be explicit, scoped, time-bound, owned, justified, reviewed, observable, and automatically expire. Represent them as structured data rather than hidden conditions in Rego. Include resource identity, policy code, environment, owner, ticket, expiry, and compensating control.

Decision logs and periodic reports should show exception use.

### Example / evidence

```text
Exception object:
{
  "policy_code":"K8S_SEC_001",
  "resource":"namespace/app",
  "expires":"2026-09-01T00:00:00Z",
  "ticket":"RISK-1234",
  "owner":"team-a"
}
```

### L3 follow-up discussion

Test expiration and resource matching, and alert before expiry rather than extending automatically.

### Production caution

Permanent wildcard exceptions convert policy enforcement into documentation theater.

---

## Q9. How do you threat-model an OPA deployment?

### Detailed answer

Identify assets—policy, reference data, decisions, credentials, logs—and actors—callers, authors, control-plane services, administrators, attackers. Analyze spoofing of input identity, policy tampering, bundle rollback, log disclosure, denial of service, stale agents, API abuse, SSRF, and enforcement bypass.

Map each threat to preventive, detective, and recovery controls.

### Example / evidence

```text
Example threats:
- caller forges tenant
- compromised bundle grants admin
- huge input exhausts memory
- decision logs leak tokens
- OPA unavailable triggers fail-open
- PEP ignores deny
- stale sidecar never updates
```

### L3 follow-up discussion

Include the enforcement point and management plane; threat-modeling OPA alone misses the most important trust boundaries.

### Production caution

A perfect policy cannot compensate for untrusted input or a bypassable enforcement path.

---

## Q10. How do security upgrades affect OPA operations?

### Detailed answer

Track OPA, embedded SDK, Gatekeeper, container image, Go runtime, and integration plugins. Review release notes for security fixes, language changes, performance regressions, and configuration warnings. Test bundles and decision behavior before promotion, but prioritize urgent fixes.

Maintain an inventory linking every deployment to runtime and active policy revision.

### Example / evidence

```text
Upgrade evidence:
- vulnerability/advisory
- affected versions
- target version and image digest
- compatibility tests
- canary metrics
- rollout status
- rollback artifact
```

### L3 follow-up discussion

A recent OPA release can contain security fixes in APIs, built-ins, or its Go toolchain, so executable and deployment exposure both matter.

### Production caution

Do not delay a security fix indefinitely because policy tests are slow; build and maintain an emergency validated upgrade path.

---

## Official references

- https://www.openpolicyagent.org/docs/security
- https://www.openpolicyagent.org/docs/operations
- https://www.openpolicyagent.org/docs/monitoring

---

<!-- Original file: 08. OPA Performance Profiling and Optimization.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Performance, Profiling, and Optimization

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Metrics, profiling, partial evaluation, entrypoints, data shape, resource limits, caching, and SLOs.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How do you measure OPA policy performance?

### Detailed answer

Measure end-to-end decision latency at the enforcement point and evaluation metrics inside OPA. Use representative input sizes, policy/data revisions, concurrency, warm and cold starts, and realistic hardware limits. Collect percentiles rather than only averages.

OPA CLI profiling identifies expensive expressions; server metrics identify request rate, latency, errors, and resource pressure.

### Example / evidence

```text
Commands:
`opa eval --metrics --profile --data policy --input input.json 'data.app.decision'`
`opa bench --data policy --input input.json 'data.app.decision'`
`opa test --bench ./policy`

Operational metrics:
p50/p95/p99 latency, allocations, CPU, memory, GC, bundle size, error rate.
```

### L3 follow-up discussion

Separate policy evaluation time from network, JSON serialization, TLS, external calls, and enforcement-point processing.

### Production caution

A microbenchmark on a tiny input does not predict admission latency for a large Kubernetes object or authorization under concurrency.

---

## Q2. What commonly makes Rego policies slow?

### Detailed answer

Common causes include repeated scans of large arrays, nested comprehensions, broad iteration before selective filtering, repeated conversion or sorting, large base documents, expensive regex or graph operations, and external HTTP calls. Poor data shape can matter more than rule count.

Profile before optimizing and validate semantic equivalence with tests.

### Example / evidence

```text
Optimization sequence:
1. capture representative slow input
2. profile expressions
3. bind selective keys early
4. index data by lookup key
5. compute reusable documents once
6. remove duplicate scans
7. benchmark and compare decisions
```

### L3 follow-up discussion

Discuss memory-versus-speed trade-offs when adding indexes or precomputed data to bundles.

### Production caution

Do not replace clear correct policy with opaque micro-optimizations unless a measured SLO requires it.

---

## Q3. How does partial evaluation improve performance?

### Detailed answer

Partial evaluation precomputes policy portions using known policy and data and leaves residual conditions for unknown input. It can reduce runtime work or produce an optimized bundle for specified entrypoints.

It is most effective when stable policy/data can be compiled ahead and a smaller set of request fields remains unknown.

### Example / evidence

```text
Build optimized bundle:
`opa build -O=2 -e company/authz/decision policy/ data/`

Evaluate compile API with unknown resource attributes for data filtering.
```

### L3 follow-up discussion

Test optimized and original decisions across a corpus because entrypoints, unknowns, and built-ins affect generated policy.

### Production caution

Optimization can increase build complexity or artifact size and may not help highly dynamic or external-data policies.

---

## Q4. What is an entrypoint, and why is it important?

### Detailed answer

An entrypoint is a document intended to be queried by an integration. Declaring entrypoints helps optimization and Wasm compilation preserve and specialize the required policy. It also makes the public policy API explicit.

Corporate repositories should maintain a catalog of entrypoint paths, input/output schemas, owners, latency budgets, and callers.

### Example / evidence

```text
Examples:
- `company/http/authz/decision`
- `company/kubernetes/violations`
- `company/terraform/deny`

Build:
`opa build -e company/http/authz/decision ...`
```

### L3 follow-up discussion

An internal helper package should not become a public entrypoint accidentally because callers then depend on implementation details.

### Production caution

Removing or renaming an entrypoint is a breaking integration change even when all Rego tests pass.

---

## Q5. How do bundle size and data shape affect runtime?

### Detailed answer

OPA keeps active policy and data in memory by default. Large bundles increase download, verification, parse, compile, activation, and memory cost. Deep or list-oriented data can make lookups slower than objects keyed by the queried identifier.

Partition data by policy domain, remove unused fields, use efficient lookup maps, and measure activation time and memory.

### Example / evidence

```text
Prefer:
`data.users_by_id["u123"]`

Over repeated scan:
`some u in data.users; u.id == "u123"`
```

### L3 follow-up discussion

Discuss whether rapidly changing or massive datasets belong in OPA at all, versus being supplied selectively in input or queried through another authorization architecture.

### Production caution

A policy-data bundle that approaches application-database scale can create slow rollout and memory exhaustion across the fleet.

---

## Q6. How do external calls affect OPA latency and reliability?

### Detailed answer

An external call adds DNS, connection, TLS, service latency, rate limit, failure, and retry behavior to every affected decision. Under concurrency, it can exhaust OPA workers or remote capacity and make policy availability depend on another system.

Cache only where correctness and revocation requirements permit, batch calls where supported, and set strict deadlines.

### Example / evidence

```text
Latency budget:
total request = PEP overhead + OPA network + evaluation + external data + serialization

Measure each component separately and test outage behavior.
```

### L3 follow-up discussion

Gatekeeper external data can batch provider references, but it remains an admission-path dependency that needs HA and timeout engineering.

### Production caution

Do not use long retries inside a synchronous policy decision; the caller's request deadline is usually shorter.

---

## Q7. How do CPU and memory limits affect OPA?

### Detailed answer

OPA evaluation, parsing, bundle activation, decision logging, and garbage collection consume CPU and memory. Too-low CPU limits can cause latency and readiness failures; too-low memory can trigger OOM during large bundle activation or concurrent evaluation.

Size using peak input, bundle, concurrency, and telemetry workload. Monitor throttling, RSS, heap, GC, and OOM events.

### Example / evidence

```text
Load test:
- old bundle active
- new large bundle activation
- peak concurrent decisions
- decision-log batching
- external call delays
- resource limits equal to production
```

### L3 follow-up discussion

Maintain headroom for simultaneous old/new policy state during update and temporary log buffering.

### Production caution

Raising memory after every OOM without analyzing policy/data growth hides an unbounded design problem.

---

## Q8. What is decision caching, and when is it safe?

### Detailed answer

Caching can occur in the enforcement point, a local proxy, or application layer. Cache keys must include every input attribute that can affect the decision, the policy revision, and relevant data version. TTL must respect revocation and risk.

Authorization caching is unsafe when permissions or resource state change faster than the cache or when keys omit tenant, action, or identity context.

### Example / evidence

```text
Cache key example:
hash(policy_revision, subject_id, authn_strength, tenant, action, resource_id, resource_version)

Invalidate on role or ownership changes where possible.
```

### L3 follow-up discussion

Distinguish caching final decisions from caching stable reference data supplied to OPA.

### Production caution

Never cache a broad allow decision using only URL path when user and tenant attributes affect policy.

---

## Q9. How do you investigate high p99 decision latency?

### Detailed answer

Correlate slow decisions by entrypoint, input size, policy revision, agent, node, external dependency, and time. Compare OPA metrics/profiles with enforcement-point traces and CPU/memory throttling. Determine whether latency comes from a small class of inputs or fleet-wide saturation.

Capture a redacted representative input and reproduce under the same bundle.

### Example / evidence

```text
Checklist:
- bundle revision
- input bytes and collection counts
- evaluation timer
- HTTP/external call metrics
- CPU throttling/GC
- concurrent request count
- decision-log backpressure
- network/TLS latency
```

### L3 follow-up discussion

Use distributed tracing or correlation IDs to split PEP-to-OPA time from evaluation and downstream calls.

### Production caution

Restarting OPA may temporarily clear pressure but destroys evidence and does not explain regression.

---

## Q10. How do you define OPA performance SLOs?

### Detailed answer

Define latency and availability per entrypoint and risk class, plus bundle freshness, evaluation error rate, decision-log delivery, and agent convergence. Admission and interactive authorization usually need stricter latency than offline IaC scanning.

Include input-size limits and load assumptions so the SLO is testable.

### Example / evidence

```text
Example indicators:
- p99 local authorization < 10 ms
- evaluation errors < 0.01%
- 99.9% agents activate approved bundle within 5 min
- no agent stale beyond 15 min
- decision-log loss below defined threshold
```

### L3 follow-up discussion

Use error budgets to prioritize policy optimization, capacity, local deployment, or control-plane resilience.

### Production caution

An SLO based only on `/health` misses slow or incorrect decisions.

---

## Official references

- https://www.openpolicyagent.org/docs/policy-performance
- https://www.openpolicyagent.org/docs/cli
- https://www.openpolicyagent.org/docs/monitoring
- https://www.openpolicyagent.org/docs/wasm

---

<!-- Original file: 09. Kubernetes Admission Control with OPA.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Kubernetes Admission Control

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Admission sequence, AdmissionReview, webhook failure, scope, HA, rollout, generated resources, and native alternatives.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. Where does OPA fit in Kubernetes admission control?

### Detailed answer

Kubernetes admission runs after authentication and authorization for API write requests and before objects are persisted. A validating or mutating admission webhook can call an OPA-based controller such as Gatekeeper. The webhook receives an AdmissionReview, evaluates policy, and returns an allow/deny response or mutation patch.

Admission does not govern ordinary read/list/watch requests. Runtime and node-level controls remain separate.

### Example / evidence

```text
Request path:
client -> authentication -> authorization -> mutating admission -> validating admission -> persistence -> controllers

Gatekeeper participates through webhook configurations and its controller/audit components.
```

### L3 follow-up discussion

Explain why Kubernetes RBAC and admission policy are complementary: RBAC decides who may request an operation, while admission can validate the resulting object and contextual rules.

### Production caution

Do not claim OPA admission replaces RBAC, Pod Security Admission, runtime security, or network policy.

---

## Q2. What is an AdmissionReview input?

### Detailed answer

AdmissionReview contains operation, resource/group/version, namespace, name, user information, the new object, and for updates the old object when provided. Gatekeeper exposes this under `input.review`.

Policies must account for CREATE, UPDATE, DELETE, CONNECT, subresources, dry-run, and differences between admission and audit input.

### Example / evidence

```text
Common references:
- `input.review.operation`
- `input.review.kind.kind`
- `input.review.object`
- `input.review.oldObject`
- `input.review.userInfo`
- `input.review.namespace`
```

### L3 follow-up discussion

Audit does not have every admission-only field, so policies intended for both paths must avoid relying exclusively on userInfo or operation context.

### Production caution

A policy tested only on Pod CREATE can behave incorrectly for Deployment templates, updates, or generated controller resources.

---

## Q3. How do failurePolicy and timeoutSeconds affect admission?

### Detailed answer

Webhook `failurePolicy` determines whether API requests are allowed or rejected when the webhook call fails or times out. `Fail` is fail-closed; `Ignore` is fail-open for infrastructure failures. `timeoutSeconds` limits webhook waiting and directly affects API latency.

Choose per risk, availability design, and compensating controls. Keep the timeout short enough to protect the API server and make the webhook highly available.

### Example / evidence

```text
Test:
- no webhook endpoints
- TLS certificate failure
- slow policy
- OPA evaluation error
- network partition
- controller rollout
- API-server retry behavior
```

### L3 follow-up discussion

Separate explicit policy deny from webhook unavailability in alerts and audit records.

### Production caution

A long webhook timeout multiplied by multiple admission webhooks can make cluster write operations appear hung.

---

## Q4. How do namespaceSelector and objectSelector reduce webhook scope?

### Detailed answer

Webhook selectors prevent unnecessary calls by filtering namespaces or object labels at the API server. This reduces load and can protect system namespaces or exempt approved workloads.

Selectors are coarse controls and must align with policy match rules. Labels used for exemption are security-sensitive and must not be freely mutable by tenants.

### Example / evidence

```text
Scope design:
- include managed tenant namespaces
- exclude Gatekeeper's own namespace carefully
- limit resource types and operations
- protect exemption labels with RBAC/admission
```

### L3 follow-up discussion

Explain the difference between API-server webhook selectors and Gatekeeper Constraint matching/exclusions.

### Production caution

A tenant-controlled label that bypasses the webhook is an enforcement bypass.

---

## Q5. What are mutating and validating admission ordering risks?

### Detailed answer

Mutating webhooks run before validating webhooks, and mutating webhooks may be reinvoked depending on configuration. Multiple mutators can interact, and the object validated may differ from the user's original submission.

Validation should evaluate the final expected object, and mutation must be idempotent and converge without repeatedly changing values.

### Example / evidence

```text
Examples:
- sidecar injection adds a container that image policy must validate
- defaulting adds fields used by later constraints
- two mutators rewrite the same image
- mutation produces a value rejected by validation
```

### L3 follow-up discussion

Use reinvocation-aware, path-specific mutations and test with all installed webhooks.

### Production caution

Admission ordering across independently managed webhooks should not be assumed beyond Kubernetes guarantees.

---

## Q6. How do you design HA for an admission webhook?

### Detailed answer

Run multiple replicas across failure domains, use readiness that proves policy availability, protect with PDB and topology spread, reserve resources, use stable service networking and certificate rotation, and monitor latency/errors. Rolling updates must retain ready endpoints.

Audit and controller workloads should not starve admission replicas.

### Example / evidence

```text
Validate:
- one pod loss
- node drain
- zone loss
- certificate rotation
- bundle/template update
- API burst
- audit load
- network policy
```

### L3 follow-up discussion

Consider priority classes and dedicated nodes only when justified by cluster architecture.

### Production caution

Three replicas on one node are not highly available.

---

## Q7. How should admission policy be rolled out safely?

### Detailed answer

Start with offline tests and scans, then dry-run or warning/audit modes, measure existing violations, create remediation and exception plans, canary selected namespaces, and finally enforce. Maintain rollback by changing enforcement action or reverting the immutable policy artifact.

Communicate denial codes and remediation to application teams.

### Example / evidence

```text
Rollout stages:
1. CI policy tests
2. audit existing resources
3. dryrun/warn
4. selected nonproduction namespaces
5. production canary
6. full deny
7. continuous audit
```

### L3 follow-up discussion

Measure denial rate and false positives by policy code and workload owner.

### Production caution

Deploying deny globally before inventory can block controllers, upgrades, and incident remediation.

---

## Q8. How do admission policies handle generated resources?

### Detailed answer

Users often create Deployments, Jobs, or custom resources while controllers create Pods. Policies can target workload templates to give earlier feedback and/or target Pods for complete coverage. Generated resources may use different users, labels, defaults, and fields.

Policy design must decide whether to validate parent, child, or both without duplicate confusing messages.

### Example / evidence

```text
Coverage examples:
- Deployment `spec.template`
- StatefulSet/DaemonSet/Job templates
- direct Pod creation
- operator-generated custom resources
- ephemeral containers/subresources
```

### L3 follow-up discussion

Use policy libraries or helpers to normalize pod-spec locations across resource kinds.

### Production caution

Validating only Pods can make a Deployment appear accepted while its Pods are repeatedly rejected by admission.

---

## Q9. How do break-glass and emergency operations work with admission?

### Detailed answer

Design a narrowly scoped, time-bound, audited exception controlled by a trusted administrative identity or protected label/namespace. The exception should preserve critical invariant policies and require incident/change linkage.

Test the path before an emergency and alert on every use.

### Example / evidence

```text
Break-glass controls:
- restricted group
- exact policy codes
- exact namespaces/resources
- expiry
- reason/ticket annotation
- post-incident review
```

### L3 follow-up discussion

Avoid disabling the webhook cluster-wide. A policy-specific exception or enforcement-action change is easier to audit and recover.

### Production caution

A permanent system:masters exemption can allow accidental or malicious bypass of every policy.

---

## Q10. How do native ValidatingAdmissionPolicy and Gatekeeper compare?

### Detailed answer

Kubernetes ValidatingAdmissionPolicy evaluates CEL in-process in the API server and avoids an external webhook call. Gatekeeper provides Rego-based constraints, audit, mutation, external data, policy libraries, and additional governance features. Gatekeeper can also integrate with or generate native ValidatingAdmissionPolicy resources for suitable policies in supported versions.

Choose based on language, portability, audit needs, external/reference data, operations, performance, and existing policy investment.

### Example / evidence

```text
Decision factors:
- in-process latency and availability
- Rego versus CEL expertise
- audit of existing resources
- mutation
- referential/external data
- multi-platform policy reuse
- policy distribution and tooling
```

### L3 follow-up discussion

A layered strategy may use native CEL for simple critical validations and Gatekeeper for broader reusable governance.

### Production caution

Do not duplicate conflicting rules in CEL and Rego without one source of truth and equivalence tests.

---

## Official references

- https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
- https://open-policy-agent.github.io/gatekeeper/website/docs/
- https://open-policy-agent.github.io/gatekeeper/website/docs/validating-admission-policy/

---

<!-- Original file: 10. Gatekeeper ConstraintTemplates Constraints and Audit.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Gatekeeper Templates, Constraints, and Audit

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Gatekeeper architecture, validation resources, matching, enforcement, audit, messages, exemptions, testing, and upgrades.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. What is Gatekeeper, and how is it related to OPA?

### Detailed answer

Gatekeeper is a Kubernetes-native policy controller that uses OPA to evaluate policies. It adds CRDs and controllers for ConstraintTemplates, Constraints, mutation, audit, external data, status, and Kubernetes admission integration.

OPA is the general policy engine; Gatekeeper is a specialized Kubernetes integration and operational model.

### Example / evidence

```text
Core components:
- webhook/controller-manager
- audit process
- ConstraintTemplate CRDs
- generated Constraint CRDs
- constraints
- config/sync and mutation resources
- metrics and status
```

### L3 follow-up discussion

Explain why Gatekeeper policy lifecycle is driven through Kubernetes resources rather than directly loading arbitrary modules through the OPA REST API.

### Production caution

Do not manage the embedded OPA as if it were a standalone shared OPA server.

---

## Q2. What is a ConstraintTemplate?

### Detailed answer

A ConstraintTemplate defines a reusable validation policy type. It contains Rego logic that emits violation objects and an OpenAPI schema for the parameters of Constraints created from the template. Applying a template creates a new constraint kind.

Templates are policy library code; Constraints are configured instances.

### Example / evidence

```text
Template responsibilities:
- unique kind name
- Rego target package
- parameter schema
- violation message/details
- compatibility and tests
- optional VAP generation settings
```

### L3 follow-up discussion

Schema validation prevents malformed parameters and improves user feedback. Structural schema requirements must match the Gatekeeper version.

### Production caution

Changing a template schema or semantics can affect every existing Constraint instance.

---

## Q3. What is a Constraint?

### Detailed answer

A Constraint is an instance of a ConstraintTemplate. It supplies parameters, matching scope, enforcement action, and metadata. Multiple Constraints can reuse one template for different environments, namespaces, severities, or allowed values.

Constraints are the policy administration layer and should be owned and promoted like configuration.

### Example / evidence

```text
Example instances:
- required labels in all production namespaces
- approved registries for regulated workloads
- maximum replicas in development
- deny privileged containers cluster-wide
```

### L3 follow-up discussion

Use names and annotations that include policy code, owner, severity, and exception process.

### Production caution

Duplicated overlapping Constraints can produce repeated violations and confusing ownership.

---

## Q4. How does the Gatekeeper `match` field work?

### Detailed answer

Match limits which resources a Constraint evaluates by kinds, API groups, namespaces, namespace selectors, label selectors, scope, and excluded namespaces depending on configuration. The matching layer should be as narrow as the policy requirement while preserving coverage.

Match and Rego conditions are both part of effective scope.

### Example / evidence

```text
Review:
- included API groups/kinds
- namespaced versus cluster-scoped
- excluded namespaces
- namespaceSelector
- object labelSelector
- generated resource kinds
- audit coverage
```

### L3 follow-up discussion

Test match behavior with `gator`, admission dry-run, and inventory queries. Namespace labels used for selection require governance.

### Production caution

A correct Rego rule does nothing for resources excluded by match.

---

## Q5. What are enforcement actions?

### Detailed answer

Gatekeeper validation constraints can deny, warn, dry-run, or apply other supported reporting behaviors depending on version and configuration. `deny` blocks matching admission requests; `warn` allows with a warning; `dryrun` records violations without blocking. Audit can report violations for existing resources.

Use enforcement action as a rollout and risk-control mechanism.

### Example / evidence

```text
Lifecycle:
dryrun -> warn/nonprod deny -> production deny

Record:
policy code, violation count, affected owners, exceptions, enforcement date.
```

### L3 follow-up discussion

Explicitly test how the selected Kubernetes client surfaces warnings and how audit reports each action.

### Production caution

Changing enforcement action is a production security change and needs review even when Rego is unchanged.

---

## Q6. How does Gatekeeper audit work?

### Detailed answer

Audit periodically evaluates existing cluster resources against Constraints and records violations. It detects resources created before policy enforcement, bypassed through outages/failurePolicy, or made noncompliant by policy changes.

Audit is asynchronous and does not remediate by itself. Results can appear in Constraint status, logs, events, or export integrations.

### Example / evidence

```text
Operational checks:
- last audit timestamp
- audit duration
- violation limit/truncation
- audit errors
- memory/CPU
- sync/reference data
- exported event delivery
```

### L3 follow-up discussion

Audit and admission inputs differ; policies requiring admission user information may not work identically in audit.

### Production caution

A clean admission denial rate does not prove existing resources are compliant; review audit results.

---

## Q7. How do you test ConstraintTemplates and Constraints locally?

### Detailed answer

Use the `gator` CLI and Gatekeeper test facilities to evaluate templates, constraints, and Kubernetes manifests outside a cluster. Combine this with Rego unit tests, schema validation, and integration tests against a representative Kubernetes/Gatekeeper version.

Local tests speed development but do not reproduce webhook ordering, API defaulting, or every synced-data behavior.

### Example / evidence

```text
Typical gates:
- YAML/schema validation
- `gator test`
- `gator verify` or supported suite workflow
- server-side dry-run in test cluster
- audit of fixture manifests
- admission allow/deny checks
```

### L3 follow-up discussion

Pin gator to the Gatekeeper release used by the cluster.

### Production caution

A policy that passes plain `opa test` may still fail as a Gatekeeper template because target input and template schema are different.

---

## Q8. How do you write useful violation messages?

### Detailed answer

Return concise messages with stable policy codes and structured details. Include the offending field, resource context, expected value, and remediation when possible. Avoid leaking secrets or dumping the whole object.

Constraint status and admission responses have size limits, so messages should remain bounded.

### Example / evidence

```text
Violation:
{
  "msg": sprintf("[K8S-IMG-001] container %q uses unapproved registry", [c.name]),
  "details": {
    "field": "spec.containers.image",
    "image": c.image
  }
}
```

### L3 follow-up discussion

Automation should key on policy code/details, not natural-language message text.

### Production caution

Do not include Secret data, environment values, or entire admission objects in messages.

---

## Q9. How do exemptions work in Gatekeeper?

### Detailed answer

Exemptions can be implemented through Constraint match exclusions, namespace selection, Gatekeeper configuration, webhook selectors, or structured policy exception data. Choose the narrowest mechanism and distinguish admission, audit, and sync behavior.

Exemption administration is a security boundary and should be restricted and audited.

### Example / evidence

```text
Exception review:
- which policies
- admission versus audit
- namespace/resource/user scope
- expiration
- who can set exemption labels
- effect on generated resources
```

### L3 follow-up discussion

Prefer one governed exception model over scattered `excludedNamespaces` and hidden Rego conditions.

### Production caution

Exempting system namespaces broadly can leave privileged high-value workloads outside policy.

---

## Q10. How do you upgrade Gatekeeper safely?

### Detailed answer

Review the supported Kubernetes versions, embedded OPA version, CRD/schema changes, feature flags, chart/manifests, release notes, and compatibility of all templates, constraints, mutation resources, external providers, and VAP integration. Back up custom resources and test the exact upgrade in nonproduction.

Preserve webhook availability during rollout and validate audit convergence afterward.

### Example / evidence

```text
Pre/post evidence:
- Gatekeeper version/image digest
- CRDs and conversion
- webhook CA/endpoints
- template status
- constraint errors/violations
- audit duration
- admission latency/error
- metrics and resource use
```

### L3 follow-up discussion

Upgrade the controller and CRDs according to supported order; verify rollback limitations if stored schemas change.

### Production caution

Deleting Gatekeeper before removing or neutralizing webhook configurations can block Kubernetes writes.

---

## Official references

- https://open-policy-agent.github.io/gatekeeper/website/docs/
- https://open-policy-agent.github.io/gatekeeper/website/docs/constrainttemplates/
- https://open-policy-agent.github.io/gatekeeper/website/docs/audit/
- https://open-policy-agent.github.io/gatekeeper/website/docs/install/

---

<!-- Original file: 11. Gatekeeper Mutation External Data Sync and VAP.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Gatekeeper Mutation, External Data, Sync, and VAP

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Mutation, external providers, replicated data, referential policy, ValidatingAdmissionPolicy, performance, and governance.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How does Gatekeeper mutation work?

### Detailed answer

Gatekeeper mutation uses mutation-specific CRDs rather than validation ConstraintTemplates and Constraints. Mutators select resources and change fields during admission before validation evaluates the final object.

Mutation policies must be idempotent, converge, and avoid conflicts with Kubernetes defaulting and other mutating webhooks.

### Example / evidence

```text
Mutation resource families include:
- AssignMetadata
- Assign
- ModifySet
- AssignImage

Test original object, mutated object, reinvocation, and final validation.
```

### L3 follow-up discussion

Use mutation for safe standard defaults, not to conceal invalid application configuration that owners need to understand.

### Production caution

Two mutators targeting the same field can conflict or create unstable results.

---

## Q2. What does idempotent mutation mean?

### Detailed answer

Applying the mutation repeatedly produces the same final object after the first successful application. Kubernetes webhook reinvocation and multiple admission passes make idempotency essential.

Use path tests and conditional matching so values are set or transformed once.

### Example / evidence

```text
Bad:
prefix image with `registry/` every time.

Good:
assign approved prefix only when image has no registry or is not already normalized.
```

### L3 follow-up discussion

Test mutation against objects already compliant, partially compliant, and mutated by other webhooks.

### Production caution

Non-idempotent mutation can produce repeated prefixes, duplicate list entries, or admission loops.

---

## Q3. What is Gatekeeper external data?

### Detailed answer

External data lets policies obtain information from provider services, such as image trust or registry metadata, without embedding every value in Gatekeeper's local data. Gatekeeper sends provider requests and uses responses during validation or mutation according to feature support.

The provider becomes part of the admission or audit dependency chain.

### Example / evidence

```text
Provider design:
- authenticated TLS/mTLS
- bounded latency
- HA replicas
- batch support
- deterministic response schema
- caching and freshness
- failure policy
- metrics and tracing
```

### L3 follow-up discussion

Separate policy logic from provider implementation and version the provider contract.

### Production caution

An unavailable or slow provider can block or weaken admission depending on failure behavior.

---

## Q4. How should external data failure be handled?

### Detailed answer

Define behavior for timeout, DNS/TLS failure, malformed response, partial keys, stale cache, and provider denial. High-risk verification may require fail-closed; lower-risk enrichment may allow a documented fallback.

Alert separately on provider infrastructure failure and explicit noncompliance.

### Example / evidence

```text
Test:
- provider returns no result
- provider returns error per key
- mixed success batch
- expired certificate
- high latency
- cache hit/miss
- provider overload
```

### L3 follow-up discussion

Use short admission-path deadlines and avoid retry storms. Consider offline synchronized data if freshness allows.

### Production caution

Do not treat “provider unavailable” as “artifact trusted.”

---

## Q5. What is data replication or sync in Gatekeeper?

### Detailed answer

Gatekeeper can replicate selected Kubernetes resources into OPA data so referential policies can compare an admission object with cluster state. Examples include namespace metadata, allowed repositories, or related objects.

Replication increases memory, watch load, RBAC scope, and staleness complexity. Select only required kinds.

### Example / evidence

```text
Review:
- sync configuration/resource
- watched GVKs
- RBAC
- cache readiness
- object count and memory
- deletion/update propagation
- audit compatibility
```

### L3 follow-up discussion

Policies must define behavior when referenced data is not yet synced or temporarily stale.

### Production caution

Do not sync Secrets or large high-churn resource sets without a strict requirement and data-risk review.

---

## Q6. What are referential constraints?

### Detailed answer

Referential policies compare a resource against other cluster data—for example, a Route referencing an approved namespace object or a workload requiring a related security profile. They depend on Gatekeeper's cache/sync and can behave differently during startup or audit.

Use stable keys and explicit missing-reference denial or fallback semantics.

### Example / evidence

```text
Test:
- reference exists
- reference missing
- reference deleted during request
- cache not ready
- cross-namespace scope
- RBAC prevents sync
```

### L3 follow-up discussion

Consider whether Kubernetes owner references, CEL parameter resources, or a controller-based design would be simpler.

### Production caution

Admission decisions based on stale cluster data can allow or deny incorrectly; monitor cache health.

---

## Q7. How does Gatekeeper integrate with ValidatingAdmissionPolicy?

### Detailed answer

Supported Gatekeeper versions can work with or generate Kubernetes ValidatingAdmissionPolicy and binding resources for compatible validation policies. This can move suitable CEL-based validation into the API server while retaining Gatekeeper policy lifecycle and audit capabilities.

Not every Rego policy can translate, especially policies using referential data, external data, or unsupported constructs.

### Example / evidence

```text
Evaluate:
- template VAP generation setting
- generated VAP/VAPBinding
- constraint parameters
- audit source
- equivalence tests
- duplicate webhook/in-process evaluation
```

### L3 follow-up discussion

Use decision-equivalence tests before changing enforcement from Rego webhook to generated CEL.

### Production caution

Do not assume automatically generated VAP has identical edge-case behavior without testing input defaults and types.

---

## Q8. How do you tune Gatekeeper performance?

### Detailed answer

Separate validating admission, mutating admission, audit, sync, and external provider load. Scale controller replicas, reserve resources, narrow webhook/Constraint match scope, optimize Rego, reduce synced objects, tune audit interval/chunking, and monitor request and evaluation metrics.

Admission latency and audit throughput have different scaling patterns.

### Example / evidence

```text
Profile:
- request count by operation/resource
- admission p95/p99
- constraint/template count
- audit duration
- synced object count
- external data latency
- OPA evaluation stats
- CPU throttling/OOM
```

### L3 follow-up discussion

Consider separate audit deployment resources so a large audit does not starve admission.

### Production caution

Increasing replicas cannot fix a policy that performs an expensive full scan of replicated cluster data per request.

---

## Q9. How do violation export and events support operations?

### Detailed answer

Gatekeeper can expose violation information through Constraint status, logs, Kubernetes events, metrics, and export mechanisms. Central export supports compliance dashboards, ownership routing, and remediation workflows.

Design deduplication, stable IDs, retention, severity, and resource lifecycle handling.

### Example / evidence

```text
Useful fields:
policy code, constraint, enforcement action, resource UID/GVK/name, namespace, message/details, first/last seen, resolved time, owner
```

### L3 follow-up discussion

Audit violation volume may be high; use bounded event generation and a scalable export sink.

### Production caution

Do not page on every individual violation without aggregation and severity rules.

---

## Q10. How do you design Gatekeeper policy ownership at enterprise scale?

### Detailed answer

Maintain reusable reviewed templates centrally and allow controlled Constraints to parameterize approved business differences. Define who owns code, parameters, exemptions, enforcement dates, and remediation.

Use GitOps, labels/annotations, catalog documentation, tests, and change review. Separate security-mandatory policies from tenant-configurable guardrails.

### Example / evidence

```text
Governance layers:
- platform policy library
- business-unit parameter sets
- environment overlays
- exception registry
- cluster deployment
- compliance reporting
```

### L3 follow-up discussion

Prevent tenants from modifying Constraints, templates, webhook scope, or exemption labels that govern their own resources.

### Production caution

A cluster with hundreds of copied templates instead of a shared library becomes inconsistent and difficult to upgrade.

---

## Official references

- https://open-policy-agent.github.io/gatekeeper/website/docs/mutation/
- https://open-policy-agent.github.io/gatekeeper/website/docs/externaldata/
- https://open-policy-agent.github.io/gatekeeper/website/docs/validating-admission-policy/
- https://open-policy-agent.github.io/gatekeeper/website/docs/metrics/

---

<!-- Original file: 12. OPA Operations Monitoring Troubleshooting and Upgrades.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Operations, Monitoring, Troubleshooting, and Upgrades

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Health, metrics, tracing, activation errors, evaluation failures, lifecycle, DR, fleet upgrades, and runbooks.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How should OPA health checks be used?

### Detailed answer

OPA exposes health endpoints that can verify the process is running and, with appropriate parameters or operational design, whether important plugins such as bundles are ready. Readiness should prevent traffic until the required policy is active; liveness should detect an unrecoverable process without creating restart loops during temporary control-plane outages.

The enforcement point should also monitor actual decision success, not only OPA health.

### Example / evidence

```text
Health design:
- startup: initial bundle loaded
- readiness: decision entrypoint available
- liveness: process responsive
- synthetic decision: known input produces expected result
- status: target bundle revision active
```

### L3 follow-up discussion

A last-known-good bundle may justify readiness during a bundle server outage, while a fresh instance with no policy should remain unready.

### Production caution

Do not make liveness depend on every remote management service; an outage can cause endless restarts and remove cached policy.

---

## Q2. What OPA metrics should be monitored?

### Detailed answer

Monitor request count, response status, decision/evaluation latency, handler latency, compile/evaluation errors, bundle download and activation, decision-log/status upload errors, Go runtime memory/GC, CPU, process restarts, and input size where available.

Group by entrypoint and agent without high-cardinality labels.

### Example / evidence

```text
Dashboards:
- decisions per second and p99
- error/undefined rate
- bundle revision and activation failures
- agent stale status
- CPU throttling, RSS, OOM/restarts
- management API backlog
- external call latency
```

### L3 follow-up discussion

Correlate enforcement-point metrics with OPA metrics because a caller can fail before or after OPA evaluation.

### Production caution

Do not use decision ID, subject ID, resource ID, or URL as Prometheus labels.

---

## Q3. How does distributed tracing help OPA operations?

### Detailed answer

Tracing connects the enforcement request, OPA API call, policy evaluation, and descendant HTTP calls with one trace. It isolates network, queue, evaluation, and external dependency latency and supports p99 incident analysis.

Propagate trace context through trusted headers and control sampling and sensitive attributes.

### Example / evidence

```text
Trace spans:
application authorization
  -> OPA HTTP request
     -> policy evaluation
        -> `http.send` external service
  -> enforcement action
```

### L3 follow-up discussion

Use decision IDs and bundle revision as low-volume span attributes where supported and appropriate.

### Production caution

Do not attach full input or decision documents to traces without data-classification approval.

---

## Q4. How do you troubleshoot bundle activation failure?

### Detailed answer

Read status/plugin errors and classify download, authentication, TLS, integrity/signature, archive structure, root overlap, Rego parse/compile, built-in capability, or memory failure. Verify the artifact independently with the same OPA version.

The previous bundle may remain active, so determine current revision and security impact of staleness.

### Example / evidence

```text
Checks:
`opa inspect bundle.tar.gz`
`opa check --strict bundle-source/`
`opa test bundle-source/`
`opa eval -b bundle.tar.gz 'data'`

Compare manifest, roots, rego_version, and checksum.
```

### L3 follow-up discussion

Stop promotion, retain the failed artifact, fix source, rebuild immutably, and validate canary activation.

### Production caution

Do not delete the last-known-good bundle from agents while investigating the new artifact.

---

## Q5. How do you troubleshoot undefined decisions?

### Detailed answer

Verify query path, input envelope, package/rule name, default rule, field types, and active bundle revision. Evaluate the exact redacted input locally and use explanation or debug tooling to identify which condition failed.

Undefined may be intentional for a helper rule but should normally be eliminated at public decision entrypoints.

### Example / evidence

```text
Evidence:
- HTTP response contains no result
- `opa eval --explain=notes/full`
- print statements in nonproduction
- schema validation
- bundle provenance
- comparison with known-good input
```

### L3 follow-up discussion

Add an explicit default or a typed wrapper decision only after confirming the intended semantics.

### Production caution

Do not convert an unexplained undefined result to allow in the application.

---

## Q6. How do you troubleshoot a Rego compile or evaluation error?

### Detailed answer

Compile errors include parse errors, unsafe variables, type errors, recursion, and rule conflicts detectable before activation. Evaluation errors include built-in failures, complete-rule conflicts for a specific input, external HTTP failures, or resource limits.

Capture file, line, error code/message, OPA version, capabilities, bundle revision, and reproducing input.

### Example / evidence

```text
Commands:
`opa fmt --fail`
`opa check --strict`
`opa test --verbose`
`opa eval --strict-builtin-errors --explain=full ...`
`regal lint`
```

### L3 follow-up discussion

Fix the earliest root error, add a regression test, and review why CI did not catch the failing input or runtime capability.

### Production caution

Do not catch every evaluation error and return false inside the PEP without alerting; errors may indicate corrupted policy.

---

## Q7. How do you handle OPA process shutdown and rolling restart?

### Detailed answer

Give requests a drain period, stop routing new traffic, allow in-flight evaluations and telemetry flush where practical, and preserve last-known-good artifacts. Use multiple replicas or sidecar-aware pod termination so application and OPA lifecycle remain coordinated.

OPA and integration timeouts must fit inside the platform termination grace period.

### Example / evidence

```text
Rolling test:
- readiness removed before termination
- in-flight requests finish or fail predictably
- decision logs/status flush or tolerate loss
- new replica loads approved bundle before ready
- no fail-open spike
```

### L3 follow-up discussion

In a sidecar pattern, consider which container receives termination first and whether the application continues calling a stopped OPA.

### Production caution

A rolling update that temporarily leaves no ready admission or authorization endpoints is an outage.

---

## Q8. How do you perform OPA disaster recovery?

### Detailed answer

Preserve source repositories, CI configuration, immutable bundles, discovery configuration, credentials recovery, policy data sources, signing keys, and control-plane databases. Distributed agents may continue with cached policy, but new instances need a recoverable artifact and configuration path.

Define RPO/RTO for policy publication, agent convergence, decision logs, and exception data.

### Example / evidence

```text
DR exercise:
- restore artifact repository
- restore discovery/control-plane config
- rotate credentials
- bootstrap a fresh OPA
- activate target revision
- verify decisions
- resume status/log ingestion
```

### L3 follow-up discussion

Test recovery when identity, DNS, or one region is unavailable, not only a normal restore.

### Production caution

Decision logs are audit evidence but should not be the only copy of policy artifacts or exception state.

---

## Q9. How do you upgrade OPA across a fleet?

### Detailed answer

Inventory runtime versions and integrations, review release notes and Rego compatibility, run source/bundle tests against the target, benchmark, update a canary group, compare decisions and resource behavior, then roll out in waves.

Keep policy compatible with old and new agents during the mixed-version window or coordinate a bundle gate.

### Example / evidence

```text
Track:
- agent binary/image digest
- embedded SDK/app version
- active bundle revision
- startup/activation errors
- p99 latency and memory
- decision differences
- rollback version
```

### L3 follow-up discussion

A two-phase approach may first deploy policies compatible with both versions, then upgrade agents, then use new language/features.

### Production caution

Do not publish policy requiring a new built-in before all consuming agents support it.

---

## Q10. What should an OPA operational runbook contain?

### Detailed answer

Include architecture and owners, entrypoints, service endpoints, current versions, bundle/discovery sources, credentials, health/metrics, decision-log paths, fail behavior, common errors, diagnostic commands, rollback, escalation, and data-handling cautions.

Runbooks must be versioned and tested by someone other than the author.

### Example / evidence

```text
Runbook scenarios:
- OPA unavailable
- bundle stale/failed
- high latency/OOM
- undefined/error spike
- certificate expiry
- decision-log outage
- emergency policy rollback
- compromised artifact
```

### L3 follow-up discussion

Link stable error codes and alerts to the exact runbook section and required approvals.

### Production caution

A command list without expected output, risk, and validation is not an L3 runbook.

---

## Official references

- https://www.openpolicyagent.org/docs/operations
- https://www.openpolicyagent.org/docs/monitoring
- https://www.openpolicyagent.org/docs/debugging
- https://www.openpolicyagent.org/docs/management-status

---

<!-- Original file: 13. OPA Enterprise Use Cases Microservices IaC and DevSecOps.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Enterprise Use Cases, Microservices, IaC, and DevSecOps

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** HTTP and mesh authorization, IaC, data access, entitlements, deployment governance, multi-cloud, and policy-as-code.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. How is OPA used for HTTP API authorization?

### Detailed answer

An API gateway or application middleware authenticates the caller, normalizes subject, action, route, resource, tenant, and context, and asks OPA for a decision. OPA implements RBAC, ABAC, ownership, relationship, environment, or risk rules and can return reasons or obligations.

Resource-specific authorization may require trusted resource attributes fetched before evaluation or residual filtering for collection endpoints.

### Example / evidence

```text
Input:
subject, authentication method, tenant, method, normalized route, action, resource attributes, request context

Decision:
allow, reasons, field filters, row filter, audit level
```

### L3 follow-up discussion

Discuss list/search authorization: checking only the endpoint is insufficient when each returned object has different access.

### Production caution

Do not pass an unverified JWT payload directly as trusted identity without signature and issuer validation at the PEP.

---

## Q2. How is OPA used with Envoy?

### Detailed answer

Envoy's external authorization filter sends request attributes to an authorization service, which can evaluate OPA policy, commonly through the OPA-Envoy integration. The decision can allow/deny and may add or modify headers according to the integration.

This supports consistent service-mesh authorization without embedding logic in every service.

### Example / evidence

```text
Operational concerns:
- ext_authz timeout/failure mode
- request body inclusion and size
- trusted downstream identity
- header normalization
- local versus centralized authorization service
- policy bundle rollout
- per-route bypasses
```

### L3 follow-up discussion

Compare network-layer identity and HTTP attributes with application resource-level authorization needs.

### Production caution

A mesh authorization allow should not replace business authorization inside the service when resource ownership is unknown at the proxy.

---

## Q3. How is OPA used for infrastructure-as-code policy?

### Detailed answer

CI tools parse Terraform plans, Kubernetes manifests, CloudFormation, or other structured artifacts and evaluate OPA policies before merge or deployment. Policies can deny prohibited resources, require tags, enforce encryption, restrict networks, and report remediation.

Offline evaluation avoids production-path latency and supports detailed violation reports.

### Example / evidence

```text
Pipeline:
generate plan -> convert to JSON -> `opa eval`/Conftest -> structured violations -> review/exception -> deploy same reviewed artifact
```

### L3 follow-up discussion

Test unknown/computed Terraform values and distinguish configuration intent from post-apply cloud state.

### Production caution

IaC policy does not detect manual drift or runtime changes unless paired with continuous audit.

---

## Q4. How is OPA used for data authorization?

### Detailed answer

OPA can decide which datasets, rows, columns, or operations a subject may access. It may return filters or obligations rather than a Boolean. Partial evaluation can produce residual conditions that a trusted query layer translates.

Correctness requires preventing query bypass and preserving policy semantics in the target datastore.

### Example / evidence

```text
Decision example:
{
  "allow": true,
  "row_filter": {"tenant_id":"t1"},
  "columns":["id","status","created_at"],
  "mask":["email"]
}
```

### L3 follow-up discussion

Discuss query rewriting, parameterization, aggregation leakage, side channels, and how filter translation is tested.

### Production caution

Never concatenate a policy-generated filter into SQL without a safe, verified translation and parameterization layer.

---

## Q5. How is OPA used for feature entitlements?

### Detailed answer

OPA can evaluate plan, account, geography, contract, environment, and feature attributes to return entitlement decisions. This centralizes business rules while keeping enforcement in applications.

Entitlement data freshness and billing consistency may differ from security authorization and need separate SLOs.

### Example / evidence

```text
Decision:
enabled, quota, reason, expiry, required upgrade

Data:
product plans, customer exceptions, rollout cohorts
```

### L3 follow-up discussion

Separate temporary feature flags from contractual entitlement where ownership and audit requirements differ.

### Production caution

Do not use fail-open entitlement behavior if it can create unbilled or legally restricted access.

---

## Q6. How does OPA support CI/CD deployment governance?

### Detailed answer

OPA evaluates deployment metadata, artifact provenance, environment, approvals, change windows, vulnerability thresholds, test evidence, and platform health. The pipeline enforces the result before promotion.

Policies should consume signed or trusted evidence, not caller-supplied claims.

### Example / evidence

```text
Checks:
- immutable artifact digest
- approved source branch
- required test/signature
- vulnerability exception
- environment approval
- maintenance window
- rollback artifact
```

### L3 follow-up discussion

Decision output can include missing evidence and remediation rather than a single opaque deny.

### Production caution

A pipeline variable `tests_passed=true` is not trustworthy unless populated by the protected CI system.

---

## Q7. How does OPA support multi-cloud governance?

### Detailed answer

A shared policy layer can express organization-wide requirements while adapters normalize AWS, Azure, GCP, Kubernetes, and SaaS resources into domain contracts. Platform-specific rules remain separate where semantics differ.

Reuse policy intent and stable codes, not forced identical resource structures.

### Example / evidence

```text
Common controls:
ownership tags, approved regions, encryption, public exposure, identity boundaries, backup, logging

Adapters:
Terraform plan, cloud API inventory, admission input
```

### L3 follow-up discussion

Discuss policy data ownership and avoiding a single enormous cross-cloud bundle.

### Production caution

A generic “encrypted=true” field can hide materially different key management and threat models across providers.

---

## Q8. How is OPA used in microservice authorization?

### Detailed answer

Each service or gateway delegates consistent coarse- and fine-grained authorization decisions to local OPA or an authorization service. Policies use authenticated workload/user identity, tenant, action, and resource context.

Distributed policy enables low latency, but schema/version consistency and local enforcement coverage must be monitored.

### Example / evidence

```text
Patterns:
- sidecar per pod
- node-local daemon
- shared namespace service
- embedded SDK
- gateway plus service checks
```

### L3 follow-up discussion

Explain why gateway-only authorization cannot protect internal calls or resource-level decisions made inside services.

### Production caution

A service that exposes an undocumented endpoint can bypass centralized route-based policy unless enforcement is comprehensive.

---

## Q9. How do you apply policy-as-code governance?

### Detailed answer

Store policy and parameters in version control, require owner review, automated tests/lint/benchmarks, immutable artifact promotion, controlled exceptions, change records, and continuous monitoring. Every policy should have intent, scope, severity, owner, remediation, and lifecycle.

Policy changes should be treated like application and security-control changes.

### Example / evidence

```text
Repository metadata:
policy ID, business requirement, code owner, input schema, entrypoint, severity, enforcement stage, exceptions, test evidence
```

### L3 follow-up discussion

Generate a policy catalog and coverage map showing which enforcement points and environments consume each policy.

### Production caution

A policy in Git but not loaded or enforced anywhere is not an effective control.

---

## Q10. When should OPA not be used?

### Detailed answer

OPA is not ideal when the requirement is simple static application logic with no cross-service policy value, when enforcement cannot supply trusted input, when decisions require transactional database operations, when massive rapidly changing data must be queried synchronously, or when the team cannot operate a policy lifecycle.

Native platform controls, database authorization, application code, CEL, IAM, or a relationship-based authorization system may fit better.

### Example / evidence

```text
Decision test:
- Is the rule truly policy?
- Is input structured and trusted?
- Is evaluation side-effect free?
- Is the decision contract stable?
- Can policy be tested/distributed/observed?
- Is Rego expertise sustainable?
```

### L3 follow-up discussion

OPA can complement rather than replace native controls; defense in depth is often the correct architecture.

### Production caution

Do not introduce OPA only to add another technology layer without a governance or reuse benefit.

---

## Official references

- https://www.openpolicyagent.org/docs/integration
- https://www.openpolicyagent.org/docs/rest-api
- https://www.openpolicyagent.org/docs/policy-performance
- https://open-policy-agent.github.io/gatekeeper/website/docs/

---

<!-- Original file: 14. Corporate Production Scenario Questions.md -->

# Open Policy Agent Corporate L3 Interview Q&A — Corporate Production Scenarios

> **Level:** Corporate / Senior / L3  
> **Reference baseline:** OPA v1.19.0 and Gatekeeper v3.23.0  
> **Policy syntax:** Rego v1  
> **Scope:** Real incidents covering integrations, bundles, Rego, security, Gatekeeper, external data, performance, caches, and audit.

## L3 response pattern

A complete answer should explain architecture, input and decision contracts, policy behavior, test evidence, failure modes, security, performance, deployment safety, observability, and rollback.

---

## Q1. OPA returns HTTP 200, but the application allows a request that should be denied.

### Detailed answer

HTTP success only proves the API request was processed. The queried decision may be undefined, false, malformed, or read from the wrong response field. The application may also invert the Boolean or default missing `result` to allow.

### Example / evidence

```text
Capture the exact query path, redacted input, raw response, client deserialization, active bundle revision, and enforcement branch. Re-evaluate locally with the same artifact.
```

### L3 follow-up discussion

Define a typed decision object with explicit default deny, validate response schema, and add integration tests for false, undefined, error, and malformed responses.

### Production caution

Do not patch only the policy if the enforcement point is ignoring the decision contract.

---

## Q2. A new bundle downloads successfully but never becomes active.

### Detailed answer

Download success is separate from parse, compile, root, capability, signature, and activation success. Status usually contains the actual error. The previous bundle may still be serving decisions.

### Example / evidence

```text
Inspect bundle status, revision, logs, manifest, roots, Rego version, required built-ins, memory, and verify the bundle with the same OPA binary.
```

### L3 follow-up discussion

Stop rollout, retain last-known-good, fix and rebuild the immutable artifact, then canary activation.

### Production caution

Do not delete the old bundle or restart every agent before identifying the activation failure.

---

## Q3. OPA agents are serving three different policy revisions.

### Detailed answer

The fleet is partially converged because of staggered rollout, failed downloads, stale caches, credential/network differences, or agents that no longer report status. Decision behavior may therefore vary by instance.

### Example / evidence

```text
Query the status inventory by agent, version, environment, last report, and bundle error. Correlate decision logs and traffic routing.
```

### L3 follow-up discussion

Freeze promotion, repair stale agents, define maximum staleness, and validate all target agents before completing rollout.

### Production caution

Do not assume load balancing makes mixed policy harmless; authorization can become nondeterministic for users.

---

## Q4. Decision logs contain bearer tokens and personal data.

### Detailed answer

The enforcement point sent excessive input and log masking was missing or incomplete. This is a security incident because logs may be replicated to central systems and retained.

### Example / evidence

```text
Stop or restrict log export if safe, preserve evidence, identify affected fields/time/consumers, revoke exposed credentials, and follow privacy incident procedures.
```

### L3 follow-up discussion

Minimize OPA input, implement and test masking, restrict log access/retention, and scan for new sensitive fields.

### Production caution

Deleting one visible log file is not sufficient when data has been forwarded, backed up, or indexed.

---

## Q5. A Rego policy compiles but returns undefined for most requests.

### Detailed answer

The public entrypoint lacks a default, input paths do not match the actual schema, field types changed, or rule conditions are too narrow. Compile success cannot prove runtime coverage.

### Example / evidence

```text
Use the exact production input and bundle, `opa eval --explain`, schema checks, decision logs, and compare with test fixtures.
```

### L3 follow-up discussion

Add explicit defaults, correct contract/schema, and add tests for real input versions and missing fields.

### Production caution

Do not let the caller map undefined to allow while the cause is unresolved.

---

## Q6. An OPA upgrade causes old policies to fail compilation.

### Detailed answer

The policy likely relies on Rego v0 syntax, deprecated built-ins, changed strictness, or capabilities not supported by the new runtime. Bundle producer metadata may not correctly declare Rego version.

### Example / evidence

```text
Run migration checks with old and new OPA, inspect release notes, manifest `rego_version`, formatter/check output, and all consumer versions.
```

### L3 follow-up discussion

Use a staged migration: make policies compatible, test mixed fleet, upgrade agents, then adopt new features.

### Production caution

Do not mass-edit policy syntax without semantic regression tests, especially multi-value rules.

---

## Q7. OPA memory usage doubled after a policy-data release.

### Detailed answer

The bundle may include a much larger dataset, duplicate indexes, expanded annotations, or policy structures that allocate more during evaluation. Activation may temporarily require old and new state simultaneously.

### Example / evidence

```text
Compare bundle size/content, memory profile, object counts, evaluation allocations, concurrency, and Go runtime metrics by revision.
```

### L3 follow-up discussion

Remove unused data, reshape keyed lookups, partition bundles, tune limits with evidence, and load-test activation.

### Production caution

Simply increasing memory can hide unbounded reference-data growth across every agent.

---

## Q8. Authorization p99 latency increased after adding `http.send`.

### Detailed answer

Policy evaluation now depends on DNS, network, TLS, remote service latency, and caching. Concurrency can create remote saturation and OPA worker/resource pressure.

### Example / evidence

```text
Trace requests, inspect `http.send` metrics, remote service p99, timeout/errors, cache behavior, OPA CPU/memory, and compare inputs.
```

### L3 follow-up discussion

Move stable data into bundles/input, add strict deadlines and allowed destinations, or redesign with local external data/cache.

### Production caution

Do not add long retries inside synchronous authorization; this amplifies latency and outages.

---

## Q9. A complete rule produces a conflict only for one customer.

### Detailed answer

Two rule branches match that customer's combination and return different values. Most tests did not cover the overlap. OPA treats this as an evaluation error, not precedence.

### Example / evidence

```text
Reproduce with the exact input/data, use explanation/profile, identify all matching rule heads, and inspect customer exception data.
```

### L3 follow-up discussion

Make conditions mutually exclusive or collect candidates and apply an explicit combining algorithm. Add the customer shape as a regression test.

### Production caution

Do not rely on source order or rename files to influence which rule wins.

---

## Q10. Gatekeeper blocks all Kubernetes writes during an upgrade.

### Detailed answer

Webhook configurations still route matching requests, but Gatekeeper endpoints, certificates, or readiness are unavailable and failurePolicy is Fail. The upgrade removed all ready replicas or changed CRDs/webhook state incorrectly.

### Example / evidence

```text
Check webhook configurations, service endpoints, pods, TLS/CA, failurePolicy, timeout, rollout history, API server errors, and PDB/topology.
```

### L3 follow-up discussion

Restore a compatible ready Gatekeeper deployment or carefully apply the approved emergency webhook procedure, then validate admission and audit.

### Production caution

Deleting webhook configurations blindly can create an unmonitored policy bypass and complicate reinstall.

---

## Q11. Gatekeeper audit reports violations, but admission allows identical new resources.

### Detailed answer

Audit and admission may use different input fields, match scope, enforcement action, webhook selectors, operations, or resource versions. The constraint may be dryrun, or admission may bypass a namespace/object.

### Example / evidence

```text
Compare Constraint match/enforcementAction, webhook selector, admission request, audit input, template logic, and generated resource path.
```

### L3 follow-up discussion

Align scope and logic, test server-side dry-run and real admission, and preserve audit for existing resources.

### Production caution

Do not assume audit violation automatically means `deny` is configured.

---

## Q12. A Gatekeeper Constraint shows an error after a template update.

### Detailed answer

The template parameter schema or Rego contract changed incompatibly, the generated CRD no longer accepts existing parameters, or the new policy has a compile/target error.

### Example / evidence

```text
Inspect ConstraintTemplate and Constraint status, CRD schema, controller logs, previous template version, and all instances.
```

### L3 follow-up discussion

Roll back the template if supported, migrate Constraints in a staged way, and add compatibility tests for every existing parameter set.

### Production caution

Template changes are library API changes affecting multiple policy instances.

---

## Q13. A namespace label allows teams to bypass Gatekeeper.

### Detailed answer

The label is used in webhook or Constraint namespace selection/exemption, and tenants have permission to modify it. This makes policy scope attacker-controlled.

### Example / evidence

```text
Inspect webhook selectors, Constraints, namespace RBAC, audit logs, label history, and resources created while exempt.
```

### L3 follow-up discussion

Restrict label mutation through RBAC/admission, remove unauthorized labels, audit affected resources, and redesign exemption governance.

### Production caution

Do not use tenant-writable labels as security bypass controls.

---

## Q14. Gatekeeper mutation repeatedly prefixes an image registry.

### Detailed answer

The mutation is not idempotent or does not detect the already normalized value. Reinvocation or multiple webhooks apply it repeatedly.

### Example / evidence

```text
Inspect mutators, webhook reinvocation, final object, other image mutators, and admission traces/tests.
```

### L3 follow-up discussion

Add path/value conditions so mutation converges, test already-compliant images, and coordinate ownership of the field.

### Production caution

Do not disable all mutation globally when one mutator is faulty unless the emergency impact requires it.

---

## Q15. An external-data provider outage blocks Pod creation.

### Detailed answer

The provider is a synchronous admission dependency and the policy or failure configuration is fail-closed. Provider HA, timeout, or cache design did not meet the API write SLO.

### Example / evidence

```text
Check provider endpoints, TLS, latency, Gatekeeper logs/metrics, request batching, timeout, cache, and explicit failure behavior.
```

### L3 follow-up discussion

Restore provider service, use a documented emergency exception only if risk-approved, and redesign HA/caching/offline data.

### Production caution

Treating provider unavailable as trusted may permit unverified images or identities.

---

## Q16. Gatekeeper admission latency is high during audit.

### Detailed answer

Audit and admission may compete for CPU, memory, OPA evaluation capacity, synchronized data, or external providers. Large constraint counts or scans can amplify both.

### Example / evidence

```text
Correlate audit start/duration with admission p99, controller CPU throttling, memory/GC, policy profile, synced object count, and external calls.
```

### L3 follow-up discussion

Separate or size audit resources, tune interval/chunking, optimize match and Rego, and keep admission replicas protected.

### Production caution

Adding replicas without fixing full-data scans can multiply memory and external load.

---

## Q17. A policy exception expired but access still works.

### Detailed answer

The exception may be cached, duplicated, loaded from a stale bundle, compared with incorrect time units, or not used by the effective entrypoint. The agent may be on an older revision.

### Example / evidence

```text
Check active revision, exception sources, decision logs, time built-ins/test, cache keys/TTL, and all overlapping allow rules.
```

### L3 follow-up discussion

Remove duplicate data, force controlled bundle convergence, invalidate caches, and add expiry boundary tests and alerts.

### Production caution

Do not extend the exception automatically just to restore expected behavior.

---

## Q18. OPA health is green, but every decision fails.

### Detailed answer

The process endpoint is responsive, but the bundle entrypoint may be missing, evaluation may error, API authorization may reject callers, or the client contract may be broken. Generic liveness is insufficient.

### Example / evidence

```text
Run a synthetic decision, inspect active bundle/status, API response codes, evaluation errors, authz policy, and caller parsing.
```

### L3 follow-up discussion

Add readiness and synthetic canary checks that evaluate a known entrypoint and validate output type.

### Production caution

Do not use `/health` alone as evidence that authorization is operational.

---

## Q19. A Terraform policy denies a value that is unknown until apply.

### Detailed answer

The plan contains unknown/computed values, and policy interpreted them as missing, null, or noncompliant. IaC policies must model unknowns explicitly and decide whether to defer, require evidence, or deny high-risk ambiguity.

### Example / evidence

```text
Inspect the JSON plan representation, type markers, policy assumptions, provider behavior, and post-apply audit controls.
```

### L3 follow-up discussion

Add helper rules for known/unknown state, return a distinct deferred violation where appropriate, and test plan variants.

### Production caution

Allowing every unknown value can bypass controls; denying every unknown can make valid plans unusable.

---

## Q20. A data-filter policy returns rows from another tenant.

### Detailed answer

The residual policy or filter translator omitted tenant context, misinterpreted OR/AND semantics, or the query path bypassed enforcement. This is a data breach risk.

### Example / evidence

```text
Stop affected access, capture subject/query/filter/policy revision, compare full OPA decision with generated SQL/query, and identify all exposed records.
```

### L3 follow-up discussion

Fix the translator with semantic equivalence tests, parameterize queries, enforce tenant at another layer, and perform incident response.

### Production caution

Do not trust a filter string because it originated from policy; translation and enforcement are security-critical code.

---

## Q21. A decision cache continues allowing a revoked user.

### Detailed answer

The cache key or invalidation strategy does not include role/credential version, policy/data revision, or revocation state; TTL exceeds the security requirement.

### Example / evidence

```text
Inspect cache key, entry age, policy revision, identity change timestamp, decision logs, and all caching layers.
```

### L3 follow-up discussion

Purge affected entries, shorten or remove cache for high-risk decisions, add version/invalidation signals, and test revocation SLO.

### Production caution

Caching authorization without a revocation design is an intentional delay in security enforcement.

---

## Q22. Decision-log upload errors consume increasing memory.

### Detailed answer

The telemetry plugin is buffering or retrying while the remote sink is unavailable or slow. Backpressure and queue limits were not sized or monitored.

### Example / evidence

```text
Check queue/batch configuration, upload errors, sink latency, OPA memory/GC, network/TLS, and whether logs are being dropped.
```

### L3 follow-up discussion

Restore sink, bound buffering, choose drop/backpressure behavior according to audit requirements, and alert before memory pressure.

### Production caution

Decision telemetry must not be allowed to OOM the policy decision service.

---

## Q23. A new policy denies Kubernetes controllers in system namespaces.

### Detailed answer

The match scope or exemption design is too broad, or generated resources differ from test fixtures. System controllers may be unable to reconcile critical components.

### Example / evidence

```text
Identify denied users/resources/namespaces/policy codes, Constraint match, webhook scope, audit inventory, and recent rollout.
```

### L3 follow-up discussion

Move enforcement to dryrun or a narrow exception through approved change, repair policy scope, and validate system reconciliation.

### Production caution

Broad permanent exclusion of all system namespaces can hide serious privileged-policy violations.

---

## Q24. A security team wants one OPA instance for every enterprise policy use case.

### Detailed answer

A single shared service appears simple but combines latency, availability, data sensitivity, tenant isolation, upgrade, and blast radius across unrelated workloads. Different entrypoints may require distinct deployment and data boundaries.

### Example / evidence

```text
Classify use cases by latency, trust zone, sensitivity, availability, data size, runtime target, and owner.
```

### L3 follow-up discussion

Use logically consistent governance with multiple fit-for-purpose OPA deployments and shared policy libraries/control plane.

### Production caution

Central governance does not require one runtime process or one failure domain.

---

## Q25. An incident review cannot prove which policy allowed a request.

### Detailed answer

Decision logs lack revision/decision ID, status inventory is incomplete, artifact history is mutable, or application logs cannot correlate the request to OPA.

### Example / evidence

```text
Gather enforcement logs, OPA logs, status, bundle artifacts, source history, deployment events, and load-balancer routing; state uncertainty explicitly.
```

### L3 follow-up discussion

Add immutable revisions, decision IDs, correlation, provenance, status retention, and audit-grade artifact storage.

### Production caution

Do not make confident authorization claims when the evidence cannot identify the evaluated policy.

---

## Official references

- https://www.openpolicyagent.org/docs/operations
- https://www.openpolicyagent.org/docs/debugging
- https://open-policy-agent.github.io/gatekeeper/website/docs/
- https://open-policy-agent.github.io/gatekeeper/website/docs/audit/
- https://www.openpolicyagent.org/docs/security

---

<!-- Original file: 15. Command and Coding Interview Lab.md -->

# Open Policy Agent Corporate L3 — Command and Coding Interview Lab

> **Baseline:** OPA v1.19.0, Rego v1, Gatekeeper v3.23.0  
> Replace sample paths and use disposable test environments for enforcement examples.

## 1. Version and capabilities

```bash
opa version
opa capabilities
opa capabilities --version v1.19.0 > capabilities-v1.19.0.json
```

**Interview expectation:** Explain why runtime version, Rego syntax, built-ins, Wasm target, embedded SDK, and Gatekeeper's embedded OPA must be tested as a compatibility matrix.

## 2. Format and compile checks

```bash
opa fmt --fail --diff ./policy
opa check --strict ./policy
regal lint ./policy
```

**Interview expectation:** Formatting, compile/type safety, and linting are separate controls. None replaces functional tests.

## 3. Basic Rego v1 authorization policy

```rego
package company.authz

default allow := false

allow if {
    input.subject.authenticated == true
    input.action == "read"
    input.subject.tenant == input.resource.tenant
}

reasons contains "cross-tenant access denied" if {
    input.subject.tenant != input.resource.tenant
}
```

## 4. Evaluate a decision

```bash
opa eval \
  --data ./policy \
  --input ./testdata/read-request.json \
  --format=pretty \
  'data.company.authz.allow'
```

**Interview expectation:** Distinguish explicit false, undefined, compile error, evaluation error, and HTTP transport success.

## 5. Metrics, explanation, and profiling

```bash
opa eval \
  --data ./policy \
  --input ./testdata/request.json \
  --metrics \
  --profile \
  --explain=notes \
  'data.company.authz.decision'
```

**Interview expectation:** Use a redacted production-shaped input, identify the expensive expression, and separate evaluation from network and serialization latency.

## 6. Unit tests and coverage

```bash
opa test --fail-on-empty --verbose ./policy
opa test --coverage --threshold 90 ./policy
opa test --format=json ./policy > test-results.json
```

Example test:

```rego
package company.authz_test

import data.company.authz

test_admin_allowed if {
    authz.allow with input as {
        "subject": {"authenticated": true, "tenant": "t1"},
        "action": "read",
        "resource": {"tenant": "t1"},
    }
}

test_cross_tenant_denied if {
    not authz.allow with input as {
        "subject": {"authenticated": true, "tenant": "t1"},
        "action": "read",
        "resource": {"tenant": "t2"},
    }
}
```

## 7. Benchmark tests

```bash
opa test --bench ./policy
opa bench \
  --data ./policy \
  --input ./testdata/large-request.json \
  'data.company.authz.decision'
```

**Interview expectation:** Benchmark representative and worst-case input under controlled CPU/memory, and compare relative regressions.

## 8. Build a bundle

```bash
opa build \
  --bundle ./bundle-src \
  --output policy-bundle.tar.gz

opa inspect policy-bundle.tar.gz
sha256sum policy-bundle.tar.gz
```

**Interview expectation:** Explain bundle roots, revision, manifest Rego version, immutable promotion, integrity, and activation status.

## 9. Build an optimized bundle

```bash
opa build \
  --optimize=2 \
  --entrypoint company/authz/decision \
  --bundle ./bundle-src \
  --output optimized-bundle.tar.gz
```

**Interview expectation:** Partial evaluation specializes declared entrypoints. Test semantic equivalence and artifact size/performance.

## 10. Build a Wasm policy

```bash
opa build \
  --target wasm \
  --entrypoint company/authz/decision \
  ./policy ./data \
  --output policy-wasm.tar.gz
```

**Interview expectation:** Explain unsupported built-ins, ABI/SDK, data loading, compiled entrypoints, artifact distribution, and update lifecycle.

## 11. Run OPA as a local server

```bash
opa run --server \
  --addr=127.0.0.1:8181 \
  ./policy ./data
```

Evaluate:

```bash
curl --fail --silent --show-error \
  --header 'Content-Type: application/json' \
  --data @request.json \
  http://127.0.0.1:8181/v1/data/company/authz/decision |
jq -e '.result | objects'
```

**Interview expectation:** Local binding does not remove the need for timeouts, typed response validation, health, and fail behavior.

## 12. Query health and metrics

```bash
curl --fail --silent http://127.0.0.1:8181/health
curl --fail --silent http://127.0.0.1:8181/metrics
```

**Interview expectation:** Generic health is not a synthetic authorization test. Readiness should account for the required policy/bundle.

## 13. REST Data API response handling

```bash
response=$(mktemp)
code=$(
  curl --silent --show-error \
    --connect-timeout 1 \
    --max-time 3 \
    --output "$response" \
    --write-out '%{http_code}' \
    --header 'Content-Type: application/json' \
    --data @request.json \
    http://127.0.0.1:8181/v1/data/company/authz/decision
)

[[ $code == 200 ]] || exit 1
jq -e 'has("result") and (.result.allow | type == "boolean")' "$response"
jq -e '.result.allow == true' "$response"
```

**Interview expectation:** Do not convert missing result, invalid type, or evaluation error into allow.

## 14. Partial evaluation through CLI

```bash
opa eval \
  --partial \
  --unknowns input.resource \
  --data ./policy \
  --input ./testdata/subject.json \
  'data.company.authz.allow'
```

**Interview expectation:** The residual query needs a correct target-language translator and cannot be concatenated unsafely into SQL.

## 15. Decision object with stable codes

```rego
package company.kubernetes

violations contains {
    "code": "K8S-SEC-001",
    "severity": "high",
    "message": sprintf("container %q is privileged", [container.name]),
    "field": "spec.containers.securityContext.privileged",
} if {
    some container in input.spec.containers
    container.securityContext.privileged == true
}

decision := {
    "allow": count(violations) == 0,
    "violations": sort([v | some v in violations]),
}
```

## 16. Gatekeeper ConstraintTemplate shape

```yaml
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata:
  name: k8srequiredlabels
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels
      validation:
        openAPIV3Schema:
          type: object
          properties:
            labels:
              type: array
              items:
                type: string
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredlabels

        violation[{"msg": msg, "details": {"missing_labels": missing}}] {
          provided := {label | input.review.object.metadata.labels[label]}
          required := {label | label := input.parameters.labels[_]}
          missing := required - provided
          count(missing) > 0
          msg := sprintf("missing required labels: %v", [missing])
        }
```

**Interview expectation:** Gatekeeper template target syntax and supported Rego version must match the installed Gatekeeper release. Validate with Gatekeeper/gator tooling, not only standalone OPA.

## 17. Gatekeeper Constraint shape

```yaml
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: production-required-labels
spec:
  enforcementAction: dryrun
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Namespace"]
  parameters:
    labels: ["owner", "cost-center"]
```

**Interview expectation:** Explain template versus instance, match scope, enforcement action, audit, rollout, and parameter schema.

## 18. Gatekeeper operational checks

```bash
kubectl get pods -n gatekeeper-system -o wide
kubectl get constrainttemplates
kubectl get constraints
kubectl describe constrainttemplate <name>
kubectl get validatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations
kubectl get events -n gatekeeper-system --sort-by=.lastTimestamp
```

## 19. Local Gatekeeper testing

```bash
gator test ./policy-tests
gator audit --filename ./manifests --constraint ./constraints
```

**Interview expectation:** Pin gator/Gatekeeper versions and include a real cluster integration test for API defaulting and webhook behavior.

## 20. High-risk anti-patterns

```text
- Treating missing OPA result as allow.
- Exposing OPA policy/data mutation APIs without authorization.
- Passing identity fields supplied by the end user.
- Using unrestricted http.send destinations.
- Publishing unsigned mutable bundles from a developer branch.
- Logging full tokens or AdmissionReview objects.
- Globally setting Gatekeeper failurePolicy=Ignore without risk approval.
- Using tenant-writable namespace labels as policy exemptions.
- Deploying deny before audit/dry-run inventory.
- Assuming source order gives Rego rule precedence.
```

For each item, the candidate should explain the exploit or outage path, the safer design, evidence, rollback, and preventive test.

---

<!-- Original file: 16. Mock Interview and Scoring Sheet.md -->

# Open Policy Agent Corporate L3 — Mock Interview and Scoring Sheet

## Scoring

Score each answer from **0 to 5**:

- **0:** No answer or unsafe answer
- **1:** Definition only
- **2:** Correct basic Rego or command knowledge
- **3:** Correct integration and troubleshooting sequence
- **4:** Includes security, performance, observability, testing, rollout, and rollback
- **5:** Adds enterprise architecture, failure domains, governance, compatibility, and incident communication

A strong L3 candidate should average **4 or above** and must not give unsafe answers involving undefined decisions, untrusted input, bundle supply chain, external calls, Kubernetes webhook outages, or policy exemptions.

## Round 1 — Architecture

1. Explain PDP, PEP, control plane, and policy information sources.
2. Central OPA versus sidecar/embedded OPA.
3. Define an input and decision contract.
4. Explain undefined versus false versus evaluation error.
5. Design fail-open/fail-closed behavior.

## Round 2 — Rego v1

6. Explain `if`, `contains`, assignment, equality, and unification.
7. Complete rule versus multi-value rule.
8. Safe variables and negation.
9. Comprehensions, `some in`, and `every`.
10. Rule conflict and explicit combining algorithms.

## Round 3 — Testing and performance

11. Build a policy test matrix.
12. Use `with` for mocks.
13. Coverage limitations.
14. Profile a slow policy.
15. Partial evaluation and optimized bundles.

## Round 4 — Integration

16. Data API versus Query API.
17. Go SDK versus Wasm versus daemon.
18. Secure OPA HTTP API.
19. Handle OPA unavailable.
20. Version caller and policy contracts.

## Round 5 — Management and security

21. Bundles, roots, and revisions.
22. Discovery, status, and decision logs.
23. Decision-log masking.
24. Policy supply-chain security.
25. Threat-model `http.send`.

## Round 6 — Kubernetes and Gatekeeper

26. Kubernetes admission sequence.
27. FailurePolicy, timeout, selectors, and HA.
28. ConstraintTemplate versus Constraint.
29. Audit versus admission.
30. Mutation, external data, and sync.

## Round 7 — Operations

31. OPA health versus synthetic decisions.
32. Troubleshoot failed bundle activation.
33. Investigate undefined/error spike.
34. Upgrade a mixed OPA fleet.
35. Recover the OPA control plane.

## Round 8 — Corporate scenarios

36. Application allows when OPA returns no result.
37. Gatekeeper blocks every API write.
38. External data outage stops Pods.
39. Decision cache ignores revocation.
40. Incident cannot identify the active policy revision.

## Mandatory red flags

Reject or heavily downgrade answers that recommend:

- Treating undefined or evaluation error as allow by default.
- Allowing callers to self-assert trusted identity or tenant attributes.
- Using source-file order as policy precedence.
- Loading production bundles from mutable, unauthenticated locations.
- Exposing OPA write/query APIs to application clients.
- Logging unmasked tokens, secrets, or full sensitive inputs.
- Using unrestricted `http.send` with user-controlled URLs.
- Deploying global Gatekeeper deny without audit/dry-run inventory.
- Setting webhook failurePolicy to Ignore without a documented risk decision.
- Using tenant-controlled labels as exemptions.
- Force-removing Gatekeeper webhooks without an emergency plan.
- Assuming Gatekeeper audit and admission inputs are identical.
- Caching allow decisions without revision and revocation design.
- Upgrading OPA or Gatekeeper without policy/template compatibility testing.

## Candidate response framework

For every scenario, answer in this order:

1. **Architecture and trust boundary**
2. **Impact and scope**
3. **Evidence and reproduction**
4. **Root-cause hypotheses**
5. **Smallest safe remediation**
6. **Rollback or recovery**
7. **Validation**
8. **Prevention, tests, and governance**
