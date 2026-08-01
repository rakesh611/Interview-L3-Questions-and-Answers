# LDAP Corporate L3 Interview Questions and Detailed Answers

**Target roles:** Senior LDAP Administrator, Directory Services Engineer, Linux L3 Administrator, IAM Engineer, Platform Engineer, SRE, and Infrastructure Security Engineer.

**Coverage:** LDAPv3 protocol, DIT and schema design, LDIF, search filters, OpenLDAP 2.6, Red Hat Directory Server 13 / 389 Directory Server concepts, Microsoft Active Directory interoperability, TLS, SASL, access controls, replication, backup, migration, performance, monitoring, and production troubleshooting.

> **L3 interview expectation:** Explain protocol flow, server behavior, validation commands, result codes, production impact, safe remediation, and rollback—not only definitions.

---

## How to Use This Guide

Answer production questions in this order:

1. Define the concept.
2. Explain the LDAP request path.
3. Show validation commands.
4. Interpret result codes and logs.
5. Explain risk and remediation.
6. State rollback and prevention.

## Product and Version Note

This guide uses the LDAPv3 RFC 4510 family and examples from:

- OpenLDAP 2.6: `slapd`, `ldapsearch`, `ldapmodify`, `ldapwhoami`, `slapcat`, `slapadd`, `slaptest`, and `cn=config`.
- Red Hat Directory Server 13 / 389 Directory Server: `dscreate`, `dsconf`, `dsctl`, and `dsidm`.
- Microsoft Active Directory: LDAP/LDAPS, Global Catalog, LDAP signing, channel binding, and interoperability.

Adapt all DNs, instance names, paths, ports, certificates, database backends, and replication settings to the actual platform.

---


# 1. LDAP Fundamentals and Protocol


## 1. What is LDAP, and how is it different from a relational database?

LDAP is a protocol and directory information model optimized for hierarchical, read-heavy information such as users, groups, devices, service accounts, policies, and application identities.

A relational database organizes rows in tables and combines data through joins. LDAP organizes entries in a **Directory Information Tree (DIT)**. Every entry has a Distinguished Name and typed attributes controlled by schema.

Key differences:

- LDAP naming is hierarchical.
- Searches specify a base DN, scope, filter, and requested attributes.
- Entries use structural and auxiliary object classes.
- LDAP defines Bind, Search, Add, Delete, Modify, Modify DN, Compare, Extended, and Unbind operations.
- Directory servers are optimized for lookup, authentication, group resolution, replication, and delegated administration.
- SQL transactions, joins, and analytics are not LDAP's primary design goal.

LDAP is the protocol. OpenLDAP, 389 Directory Server, Red Hat Directory Server, Active Directory, and other products are implementations.


## 2. Explain the LDAPv3 client request flow.

A typical session is:

1. The client resolves the server name.
2. It opens TCP to port 389 or commonly 636.
3. With StartTLS, it sends the StartTLS extended operation and upgrades the connection before credentials.
4. It performs anonymous, simple, or SASL Bind.
5. It sends Search or update operations.
6. The server evaluates schema, authentication state, ACLs, limits, indexes, and backend data.
7. The server returns entries, references, controls, and a final LDAP result code.
8. The client sends Unbind or closes the connection.

Troubleshooting must separate DNS, TCP, TLS, Bind, authorization, search, and application interpretation.


## 3. What are the core LDAP protocol operations?

Important operations are:

- **Bind:** Establish authentication state.
- **Unbind:** Terminate the session.
- **Search:** Retrieve entries and attributes.
- **Add:** Create an entry.
- **Delete:** Remove an entry.
- **Modify:** Add, delete, replace, or sometimes increment attributes.
- **Modify DN:** Rename or move an entry.
- **Compare:** Check whether an entry contains an attribute value.
- **Abandon:** Request abandonment of an outstanding operation.
- **Extended operation:** Add capabilities such as StartTLS and password modification.

Command-line mappings:

```bash
ldapwhoami
ldapsearch
ldapadd
ldapmodify
ldapdelete
ldapmodrdn
ldapcompare
```


## 4. What is an LDAP Bind?

Bind establishes the authentication state of a connection.

Common forms:

- Anonymous Bind
- Simple Bind with identity and password
- SASL Bind, such as GSSAPI or EXTERNAL

Example:

```bash
ldapwhoami -x   -H ldaps://ldap01.example.com   -D 'uid=svc-app,ou=services,dc=example,dc=com'   -W
```

Successful output may be:

```text
dn:uid=svc-app,ou=services,dc=example,dc=com
```

A successful Bind proves authentication only. It does not prove permission to search, read attributes, update entries, or change passwords.


## 5. What is the difference between authentication and authorization identity?

The authentication identity proves which credentials were accepted. The authorization identity is the identity whose privileges are evaluated.

They are normally the same, but SASL proxy authorization and vendor-specific proxy controls can make them different.

Validate:

```bash
ldapwhoami -Y GSSAPI -H ldap://ldap01.example.com
```

When Bind succeeds but Search returns `insufficientAccessRights`, verify the effective authorization identity.


## 6. Compare LDAP, LDAPS, and StartTLS.

- `ldap://host:389` is LDAP over TCP and may be plaintext.
- StartTLS begins on 389 and upgrades the same connection with TLS.
- `ldaps://host:636` establishes TLS before LDAP messages.

Both StartTLS and LDAPS can be secure when TLS is mandatory, certificate validation is correct, and weak protocol versions are disabled.

```bash
# Require StartTLS
ldapsearch -x -ZZ -H ldap://ldap01.example.com   -D 'uid=svc,ou=services,dc=example,dc=com' -W   -b 'dc=example,dc=com' '(uid=rakesh)'

# LDAPS
ldapsearch -x -H ldaps://ldap01.example.com   -D 'uid=svc,ou=services,dc=example,dc=com' -W   -b 'dc=example,dc=com' '(uid=rakesh)'
```

`-ZZ` fails closed when StartTLS cannot be established. Never send a simple-bind password over unprotected LDAP.


## 7. What is an LDAP URL?

General form:

```text
ldap[s]://host:port/baseDN?attributes?scope?filter?extensions
```

Example:

```text
ldaps://ldap.example.com/ou=people,dc=example,dc=com?cn,mail?sub?(uid=rakesh)
```

The URL can encode server, base DN, requested attributes, search scope, filter, and extensions. Special characters require URL percent encoding.


## 8. Explain LDAP search scopes.

Scopes are:

- `base`: Only the base entry.
- `one`: Immediate children.
- `sub`: Base and all descendants.
- Some implementations expose subordinate scope, which excludes the base.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b 'ou=people,dc=example,dc=com' -s one   '(objectClass=inetOrgPerson)' dn
```

A broad subtree search with an unindexed filter can create severe CPU and I/O load.


## 9. What is the root DSE?

The root DSE is a special entry with an empty DN that publishes server capabilities and naming information.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b '' -s base '(objectClass=*)' '*' '+'
```

Useful attributes can include:

- `namingContexts`
- `supportedControl`
- `supportedExtension`
- `supportedFeatures`
- `supportedLDAPVersion`
- `supportedSASLMechanisms`
- `subschemaSubentry`

Capability discovery should use the root DSE instead of assuming vendor support.


## 10. What is the subschema subentry?

The subschema subentry exposes object classes, attribute types, syntaxes, and matching rules.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b '' -s base subschemaSubentry

ldapsearch -x -H ldaps://ldap01.example.com   -b 'cn=subschema' -s base   objectClasses attributeTypes matchingRules ldapSyntaxes
```

The exact administrative storage of schema is vendor-specific even though LDAP schema concepts are standardized.


## 11. What is an LDAP control?

A control changes the semantics of an LDAP operation. It contains an OID, criticality, and optional value.

Examples:

- Simple paged results
- Server-side sorting
- Assertion
- Password-policy response
- Proxy authorization
- ManageDsaIT
- Synchronization or persistent-search controls

If a critical control is unsupported, the server must fail the operation. Application troubleshooting should identify controls because a plain `ldapsearch` may not reproduce an application's request.


## 12. What are LDAP referrals?

A referral tells the client that another LDAP URL may hold the requested data.

Client behavior can be:

- Follow automatically
- Return the referral to the application
- Ignore it
- Fail because credentials or trust are invalid
- Enter a loop because topology is wrong

Verify DNS, TLS trust, credential reuse, and referral-chasing policy. Blind referral chasing can leak credentials to unintended endpoints.


## 13. What is an LDAP alias?

An alias is an entry that points to another entry. Search operations can control whether aliases are dereferenced while locating the base or processing the search.

Aliases are different from referrals. Aliases redirect within directory naming, while referrals provide LDAP URLs that clients may follow.

Aliases are less common in modern designs because they complicate ACLs, loops, search results, and application behavior.


## 14. What is the difference between a suffix and a base DN?

A suffix or naming context is a server-side database boundary, such as:

```text
dc=example,dc=com
```

A base DN is the starting point a client selects for a particular operation:

```text
ou=people,dc=example,dc=com
```

One server can host multiple suffixes. Result code 32, `noSuchObject`, often indicates the requested base DN does not exist.


## 15. Why is LDAP case behavior not universally case-insensitive?

Comparison behavior comes from schema matching rules.

Examples:

- `caseIgnoreMatch`
- `caseExactMatch`
- Distinguished Name matching
- Numeric matching
- Generalized time matching
- Binary octet matching

Attribute names are generally case-insensitive, but values are compared according to the attribute's matching rule. Applications must not substitute their own lowercase logic for server matching behavior.


# 2. DIT, Naming, Schema, Attributes, and LDIF


## 16. Explain DN, RDN, and Attribute Value Assertions.

A **Distinguished Name (DN)** uniquely identifies an entry:

```text
uid=rakesh,ou=people,dc=example,dc=com
```

The leftmost component, `uid=rakesh`, is the Relative Distinguished Name relative to the parent. Each `attribute=value` component is an Attribute Value Assertion.

An RDN can be multi-valued:

```text
cn=Rakesh Jha+uid=rakesh,ou=people,dc=example,dc=com
```

Never parse a DN by splitting on commas because commas and other characters may be escaped.


## 17. How are special characters escaped in a DN?

Characters such as comma, plus, quote, backslash, angle brackets, semicolon, and equal sign can require escaping. Leading or trailing spaces and a leading `#` also require special handling.

Example:

```text
cn=Smith\, John,ou=people,dc=example,dc=com
```

Use the LDAP library's DN builder and parser rather than hand-built strings.


## 18. What is an LDAP object class?

An object class defines entry type and allowed attributes.

Kinds:

- **Structural:** Primary entry type.
- **Auxiliary:** Adds capabilities or attributes.
- **Abstract:** Used as a superclass.

Example:

```ldif
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
```

Schema defines required `MUST` and optional `MAY` attributes. Invalid combinations can return `objectClassViolation`.


## 19. What is an attribute type?

An attribute type definition includes its OID and name, syntax, matching rules, single- or multi-value behavior, operational usage, and user-modification rules.

Enterprise custom attributes require a unique OID strategy, documented semantics, compatibility testing, and schema deployment to every relevant server.


## 20. What are user and operational attributes?

User attributes hold application or identity data. Operational attributes are maintained by the directory and are often omitted from normal `*` searches.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b 'uid=rakesh,ou=people,dc=example,dc=com'   -s base '(objectClass=*)' '*' '+'
```

Operational data can include timestamps, entry UUIDs, creators, modifiers, replication state, and password-policy state.


## 21. Explain single-valued and multi-valued attributes.

Schema can mark an attribute `SINGLE-VALUE`. Otherwise it may have multiple values:

```ldif
mail: rakesh@example.com
mail: r.jha@example.com
```

LDAP generally does not guarantee value order. Applications should not assume the first value is primary unless the data model explicitly defines it.


## 22. Explain equality, substring, ordering, approximate, and presence matching.

Examples:

```text
(uid=rakesh)       Equality
(cn=Rak*)          Substring
(loginCount>=10)   Ordering, if schema supports it
(cn~=Rakesh)       Approximate, implementation dependent
(mail=*)           Presence
```

Filter operators are meaningful only when the attribute syntax and matching rules support them.


## 23. What is LDIF?

LDIF represents directory entries and change records.

```ldif
dn: uid=rakesh,ou=people,dc=example,dc=com
objectClass: top
objectClass: inetOrgPerson
uid: rakesh
cn: Rakesh Jha
sn: Jha
mail: rakesh@example.com
```

Modify record:

```ldif
dn: uid=rakesh,ou=people,dc=example,dc=com
changetype: modify
replace: mail
mail: r.jha@example.com
-
add: description
description: Senior LDAP Administrator
```

Blank lines separate records. Folded continuation lines start with one space.


## 24. Why does an LDIF value use double colon?

A double colon means base64 encoding:

```ldif
description:: UmFrZXNoIEpoYQ==
```

Decode:

```bash
printf '%s' 'UmFrZXNoIEpoYQ==' | base64 -d
```

Base64 is not encryption.


## 25. Compare `ldapadd` and `ldapmodify`.

`ldapadd` commonly adds entries. `ldapmodify` processes LDIF change records.

```bash
ldapadd -x -ZZ -H ldap://ldap01.example.com   -D 'uid=delegated-admin,ou=services,dc=example,dc=com'   -W -f add-user.ldif

ldapmodify -x -ZZ -H ldap://ldap01.example.com   -D 'uid=delegated-admin,ou=services,dc=example,dc=com'   -W -f modify-user.ldif
```

Use delegated identities instead of an unrestricted directory manager for routine work.


## 26. Explain add, delete, and replace in an LDIF modification.

```ldif
dn: uid=rakesh,ou=people,dc=example,dc=com
changetype: modify
add: telephoneNumber
telephoneNumber: +91 11 5555 1000
-
delete: description
description: Old value
-
replace: title
title: Senior Directory Services Engineer
```

Use assertion controls or optimistic concurrency when multiple provisioning systems can update the same entry.


## 27. What is Modify DN, and why can it be risky?

Modify DN renames an RDN and can move an entry under a new parent.

```bash
ldapmodrdn -x -ZZ -H ldap://ldap01.example.com   -D 'uid=delegated-admin,ou=services,dc=example,dc=com' -W   'uid=rakesh,ou=people,dc=example,dc=com'   'uid=rakesh.jha' -r
```

Risks include cached DNs, changed ACL behavior, stale DN-valued references, expensive subtree moves, and replication load.


## 28. How do you design a good DIT?

Use stable administrative boundaries, avoid frequently changing reporting structure in DNs, separate object populations where lifecycle or ACLs differ, and document naming and uniqueness rules.

Example:

```text
dc=example,dc=com
├── ou=people
├── ou=groups
├── ou=services
├── ou=devices
└── ou=applications
```


## 29. Why might employee number be a poor RDN?

An employee number can be missing for contractors, reassigned, changed during HR migration, sensitive, or non-unique after mergers.

Many enterprises use an immutable generated identifier internally and retain employee number, username, and email as attributes.


## 30. How do you manage custom schema safely?

Reuse standard schema where possible, allocate unique OIDs, define syntax and matching rules, test validation and search behavior, deploy schema before dependent data, and document version and rollback.

Never remove a schema element while entries still use it. Inconsistent schema can break replication.


# 3. Search Filters, Controls, Referrals, and Result Codes


## 31. Explain LDAP search filter syntax.

Common filters:

```text
(uid=rakesh)
(mail=*)
(cn=Rak*)
(&(objectClass=inetOrgPerson)(uid=rakesh))
(|(uid=rakesh)(mail=rakesh@example.com))
(!(accountStatus=disabled))
```

Filters use prefix notation and every component is enclosed in parentheses.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b 'ou=people,dc=example,dc=com'   '(&(objectClass=inetOrgPerson)(uid=rakesh))'   dn cn mail
```

Quote filters in the shell.


## 32. How do you prevent LDAP injection?

Never concatenate raw user input into a filter. RFC 4515 escaping includes:

- `*` as `\2a`
- `(` as `\28`
- `)` as `\29`
- Backslash as `\5c`
- NUL as `\00`

Use the LDAP library's dedicated filter-escaping function. DN escaping and filter escaping are not interchangeable.


## 33. What is an extensible match filter?

Extensible matching can name a matching-rule OID, attribute type, and whether DN attributes participate.

Examples:

```text
(attribute:matchingRuleOID:=value)
(attribute:dn:matchingRuleOID:=value)
(:matchingRuleOID:=value)
```

It is powerful but can be non-portable and expensive. Confirm server support and index behavior.


## 34. How do paged LDAP searches work?

The Simple Paged Results control returns a page of entries and a cookie. The client sends the cookie in the next request until the server returns an empty cookie.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -E pr=500/noprompt   -b 'ou=people,dc=example,dc=com'   '(objectClass=inetOrgPerson)' dn uid
```

Paging is not a guaranteed snapshot while data changes and does not bypass server security or administrative limits.


## 35. What is server-side sorting?

Server-side sorting requests ordered search results by one or more attributes.

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -E 'sss=sn:cn'   -b 'ou=people,dc=example,dc=com'   '(objectClass=inetOrgPerson)' cn sn
```

LDAP does not guarantee a natural entry order. Sorting can be expensive without suitable matching rules and indexes.


## 36. What is the assertion control?

The assertion control makes an operation conditional on a filter matching the target entry at execution time.

Use cases:

- Modify only if `modifyTimestamp` has not changed.
- Disable only if the account is currently enabled.
- Prevent lost updates from concurrent provisioning.

It is an optimistic-concurrency mechanism and should be critical when the condition is mandatory.


## 37. Explain important LDAP result codes.

Common result codes:

- `0 success`
- `3 timeLimitExceeded`
- `4 sizeLimitExceeded`
- `8 strongerAuthRequired`
- `10 referral`
- `16 noSuchAttribute`
- `19 constraintViolation`
- `20 attributeOrValueExists`
- `21 invalidAttributeSyntax`
- `32 noSuchObject`
- `34 invalidDNSyntax`
- `49 invalidCredentials`
- `50 insufficientAccessRights`
- `51 busy`
- `52 unavailable`
- `53 unwillingToPerform`
- `65 objectClassViolation`
- `68 entryAlreadyExists`
- `80 other`

Always inspect the diagnostic message and server logs. Result code 49 can represent several authentication failure causes.


## 38. How does an empty successful search differ from `noSuchObject`?

An empty successful search means the base existed but no entry matched.

`noSuchObject` usually means the base DN did not exist or could not be resolved.

Test the base:

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b 'ou=people,dc=example,dc=com'   -s base '(objectClass=*)' dn
```


## 39. What causes `sizeLimitExceeded`?

Possible causes include global limits, per-user limits, client-requested limits, administrative policy, or a broad search.

Remediate by narrowing base, scope, filter, and attributes, or by using supported paging. Do not raise the global limit merely to accommodate one poor application query.


## 40. What causes `timeLimitExceeded`?

Causes include unindexed searches, large candidate sets, slow storage, server overload, expensive ACL evaluation, group computation, lock contention, and referrals.

Use access logs and monitoring to determine where time is spent. Raising the limit can increase resource exhaustion.


## 41. What does `unwillingToPerform` mean?

The server understood the request but refuses it because of policy or state.

Examples:

- Insecure password change
- Write attempted on a read-only consumer
- Protected entry
- Unsupported subtree move
- Missing required control
- Server maintenance state

Read the server diagnostic message before retrying.


## 42. What is ManageDsaIT?

ManageDsaIT tells the server to treat referral or other special knowledge entries as normal entries for administrative operations.

It is intended for directory administration and migration tools, not routine application searches.


## 43. How do you reproduce an application search accurately?

Match all of these:

- Host and port
- TLS mode and CA trust
- Bind identity or SASL mechanism
- Base DN and scope
- Filter
- Requested attributes
- Controls
- Referral handling
- Time and size limits

Example:

```bash
LDAPTLS_CACERT=/etc/pki/ca-trust/source/anchors/corp-ca.pem ldapsearch -x -ZZ -H ldap://ldap01.example.com   -D 'uid=svc-app,ou=services,dc=example,dc=com' -W   -b 'ou=people,dc=example,dc=com' -s sub   '(&(objectClass=inetOrgPerson)(uid=rakesh))'   dn uid cn mail
```


# 4. OpenLDAP and Directory Server Administration


## 44. Explain OpenLDAP `cn=config`.

Modern OpenLDAP commonly stores online configuration in the `cn=config` DIT.

```bash
ldapsearch -Q -Y EXTERNAL -H ldapi:///   -b 'cn=config' -s one   '(objectClass=olcDatabaseConfig)'   dn olcDatabase olcSuffix
```

Benefits include LDAP-based validation and online modification. Direct editing under the slapd configuration directory is unsafe because files are generated and can be corrupted.

Back up `cn=config` before changes.


## 45. How do you identify OpenLDAP databases and suffixes?

```bash
ldapsearch -Q -Y EXTERNAL -H ldapi:///   -b 'cn=config'   '(objectClass=olcDatabaseConfig)'   dn olcDatabase olcSuffix olcRootDN olcDbDirectory
```

Example:

```text
dn: olcDatabase={1}mdb,cn=config
olcSuffix: dc=example,dc=com
olcRootDN: cn=admin,dc=example,dc=com
```

Database numbering varies, so discover the actual configuration DN before modifying it.


## 46. How do you modify OpenLDAP online configuration?

Example:

```ldif
dn: olcDatabase={1}mdb,cn=config
changetype: modify
replace: olcSizeLimit
olcSizeLimit: 5000
```

Apply and verify:

```bash
ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f change-limit.ldif

ldapsearch -Q -Y EXTERNAL -H ldapi:///   -b 'olcDatabase={1}mdb,cn=config'   -s base olcSizeLimit
```

Prepare a rollback LDIF and confirm whether the setting is global or database-specific.


## 47. Compare `slapcat` and an `ldapsearch` export.

`slapcat` reads a local backend directly and is suitable for administrative backup/export:

```bash
sudo slapcat -n 0 -l config-backup.ldif
sudo slapcat -b 'dc=example,dc=com' -l data-backup.ldif
```

`ldapsearch` uses the live protocol and is affected by ACLs, size/time limits, selected attributes, referrals, and controls.

A normal search export may omit operational or hidden data and is not automatically a complete disaster-recovery backup.


## 48. What is `slapadd`?

`slapadd` loads LDIF directly into an OpenLDAP backend, normally in maintenance mode.

```bash
sudo systemctl stop slapd
sudo slapadd -b 'dc=example,dc=com' -l data-backup.ldif
sudo chown -R ldap:ldap /var/lib/ldap
sudo systemctl start slapd
```

Paths and ownership vary.

Risks include wrong suffix, schema mismatch, stale indexes, incorrect ownership, duplicate entries, and replication identity conflicts. Follow the documented restore or replica-bootstrap process.


## 49. What is `slaptest`?

`slaptest` validates OpenLDAP configuration:

```bash
sudo slaptest -u
```

A typical successful result is:

```text
config file testing succeeded
```

This does not prove TLS trust, database integrity, replication, ACL correctness, or runtime health. It is one validation step.


## 50. What is `slapindex`?

`slapindex` rebuilds backend indexes after index configuration changes or under a recovery procedure.

Typical pattern:

```bash
sudo systemctl stop slapd
sudo slapindex -b 'dc=example,dc=com'
sudo chown -R ldap:ldap /var/lib/ldap
sudo systemctl start slapd
```

Plan disk space, maintenance duration, ownership, and rollback. Use the product-supported online/offline method.


## 51. What is the OpenLDAP MDB backend?

MDB is OpenLDAP's modern primary backend based on LMDB and memory-mapped storage.

Operational points:

- Monitor database map-size headroom.
- Monitor filesystem capacity and I/O latency.
- Use supported backup methods.
- Do not copy live backend files blindly.
- Understand that map-size exhaustion and filesystem-full conditions are different failures.

Reads are efficient, but storage correctness remains critical.


## 52. How do you troubleshoot an OpenLDAP startup failure?

```bash
systemctl status slapd
journalctl -u slapd --since '30 min ago'
ss -lntp | grep -E ':389|:636'
sudo slaptest -u
df -h
df -i
```

Check:

- Invalid configuration
- Module load failure
- Certificate/key permissions
- Port conflict
- Database ownership
- Full or read-only filesystem
- MDB map-size limit
- SELinux denial
- Invalid listener URL
- Resource limits

Do not delete database files or lock files without a supported recovery plan.


## 53. Explain `dscreate`, `dsconf`, `dsctl`, and `dsidm`.

For 389 Directory Server and Red Hat Directory Server:

- `dscreate`: Create an instance.
- `dsconf`: Configure a running instance.
- `dsctl`: Local instance lifecycle, backup, restore, and health operations.
- `dsidm`: Higher-level identity and entry management.

```bash
dsctl -l
dsctl slapd-example status
dsconf slapd-example config get
dsconf slapd-example backend suffix list
dsctl slapd-example healthcheck
```

Use `--help` because subcommand syntax varies by version.


## 54. How do you create a Directory Server instance safely?

Plan hostname, instance name, ports, suffix, root DN, storage, TLS, password handling, and replication role.

Typical process:

```bash
sudo dscreate from-file /root/instance.inf
sudo dsctl slapd-example status
sudo dsctl slapd-example healthcheck
```

Then configure TLS, indexes, password policy, access controls, monitoring, backup, and replication. Protect the INF file if it contains credentials.


## 55. How do you list and inspect suffixes in RHDS or 389 DS?

```bash
dsconf slapd-example backend suffix list

dsconf slapd-example backend suffix get   'dc=example,dc=com'
```

Prefer supported administration tools over editing low-level database configuration manually.


## 56. What does a Directory Server health check provide?

A health check can identify common reliability, security, replication, and performance issues.

```bash
dsctl slapd-example healthcheck
```

Checks may cover:

- TLS strength and certificate expiry
- Replication and time skew
- Disk space
- Indexing
- Database configuration
- Transparent Huge Pages
- Filesystem and operating-system settings

Review every finding in context before changing production tuning.


## 57. How do you add an LDAP index safely?

Workflow:

1. Identify a real slow filter.
2. Confirm attribute syntax and matching rules.
3. Select only needed index types.
4. Back up.
5. Add index configuration.
6. Reindex existing entries.
7. Validate search logs and latency.
8. Measure write amplification and database growth.

Conceptual RHDS/389 DS commands:

```bash
dsconf slapd-example backend index add   --attr uid --index-type eq,pres   'dc=example,dc=com'

dsconf slapd-example backend index reindex   --attr uid   'dc=example,dc=com'
```

Confirm exact syntax with installed-version help.


## 58. How do you validate Linux service and protocol health?

```bash
systemctl status slapd
systemctl status dirsrv@slapd-example
journalctl -u slapd
journalctl -u dirsrv@slapd-example
ss -lntp | grep -E ':389|:636'
```

Then perform protocol tests:

```bash
ldapwhoami -x -H ldaps://ldap01.example.com   -D 'uid=monitor,ou=services,dc=example,dc=com' -W

ldapsearch -x -H ldaps://ldap01.example.com   -b '' -s base namingContexts
```

A systemd Active state alone is insufficient.


# 5. Authentication, TLS, SASL, ACLs, and Security Hardening


## 59. Why is simple Bind dangerous without TLS?

Simple Bind depends on transport security for confidentiality. Without TLS, credentials can be exposed to anyone able to observe the connection.

Controls:

- Require StartTLS or LDAPS.
- Refuse cleartext simple Bind.
- Validate server certificates and hostnames.
- Disable unnecessary anonymous access.
- Use least-privilege service accounts.
- Rotate credentials and monitor failures.

Do not use `LDAPTLS_REQCERT=never` as a production workaround.


## 60. How do you troubleshoot an LDAP TLS handshake failure?

Test LDAPS:

```bash
openssl s_client   -connect ldap01.example.com:636   -servername ldap01.example.com   -showcerts </dev/null
```

Test StartTLS:

```bash
openssl s_client   -connect ldap01.example.com:389   -starttls ldap   -servername ldap01.example.com   -showcerts </dev/null
```

Validate certificate dates, SAN, issuer chain, trust store, server key permissions, TLS versions, time synchronization, and load-balancer behavior.


## 61. What causes certificate verification failure?

Common causes:

- Missing root or intermediate CA
- Hostname absent from SAN
- Expired or not-yet-valid certificate
- Wrong client trust store
- Load balancer presents another certificate
- Server omits the intermediate chain
- Client clock is incorrect
- Certificate purpose is incompatible

Correct the trust chain or certificate. Do not permanently disable verification.


## 62. How do you rotate an LDAP certificate safely?

1. Inventory all DNS names, clients, ports, and trust stores.
2. Issue a certificate with required SANs and key usage.
3. Install it with protected private-key permissions.
4. Maintain CA overlap where possible.
5. Reload or restart one replica at a time.
6. Validate StartTLS and LDAPS from multiple client platforms.
7. Monitor Bind and TLS errors.
8. Complete remaining replicas.
9. Retire the old certificate after acceptance.
10. Confirm expiry alerting.

Ensure an HA load balancer does not mix trusted and untrusted backends.


## 63. What is SASL in LDAP?

SASL is an authentication framework used by LDAP Bind. Mechanisms can include GSSAPI, EXTERNAL, and others supported by the server.

Discover mechanisms:

```bash
ldapsearch -x -H ldaps://ldap01.example.com   -b '' -s base supportedSASLMechanisms
```

Kerberos example:

```bash
ldapwhoami -Y GSSAPI -H ldap://ldap01.example.com
```

Availability depends on the server build, libraries, Kerberos configuration, and identity mapping.


## 64. How does SASL EXTERNAL work with `ldapi:///`?

A local Unix-domain LDAP socket can derive identity from operating-system credentials.

```bash
ldapwhoami -Q -Y EXTERNAL -H ldapi:///
```

A common OpenLDAP identity resembles:

```text
dn:gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
```

ACLs can grant this identity access to `cn=config`. Local root access remains highly privileged.


## 65. What is SASL identity mapping?

A SASL mechanism authenticates an external identity such as a Kerberos principal, certificate subject, or Unix credential. The server maps it to an LDAP authorization identity.

Incorrect mappings can grant excessive access, merge unrelated users, or fail after realm and certificate changes.

Validate the mapped identity with `ldapwhoami` before troubleshooting ACLs.


## 66. How do OpenLDAP ACLs work conceptually?

ACL evaluation considers the target entry and attribute, requester identity, operation, group membership, DN pattern, and sometimes transport security.

Access levels can include none, disclose, auth, compare, search, read, write, and manage.

Rule order matters. A broad rule placed earlier can prevent a later rule from taking effect. Test the exact service identity and exact operation.


## 67. Why are `auth`, `search`, `compare`, and `read` permissions different?

- `auth` permits an attribute to participate in authentication without exposing it.
- `search` permits filter evaluation.
- `compare` permits Compare operations.
- `read` permits returning the value.
- `write` permits modification.

An application may need to search by `uid` and read `mail` but must not read password hashes.


## 68. What is the difference between root DN and a delegated administrator?

The database root DN or Directory Manager commonly has unrestricted authority and may bypass normal ACLs. It is for recovery and initial administration.

A delegated administrator is governed by ACLs.

Corporate practice:

- Use delegated roles for daily work.
- Protect root credentials offline.
- Audit root usage.
- Do not configure applications with root DN.
- Test break-glass access periodically.


## 69. How should anonymous LDAP access be handled?

Anonymous access must be explicitly limited.

Potential uses:

- Root DSE discovery
- Public directory data
- Minimal health checks

Risks:

- User enumeration
- Group-membership disclosure
- Schema and topology disclosure
- Search amplification
- Privacy violations

Apply restrictive ACLs, rate limits, and monitoring.


## 70. How do you protect password attributes?

- Deny read access to password hashes.
- Require TLS for password changes.
- Use server-supported password hashing.
- Restrict reset delegation.
- Enforce password policy.
- Protect backups and replication changelogs.
- Avoid exporting password hashes in routine reports.
- Audit resets without logging passwords.

Applications normally need Bind verification, not hash access.


## 71. What is the Password Modify extended operation?

It provides a standardized password-change mechanism and may support user changes, administrative resets, and server-generated passwords.

Advantages can include password-policy integration and server-controlled hash generation.

Require TLS and distinguish a user change from an administrative reset because policy behavior may differ.


## 72. What is LDAP password policy?

Policy may define minimum quality, history, expiry, warning, failed-attempt lockout, grace logins, reset-required state, inactivity, and administrative reset behavior.

Applications should process password-policy controls and messages instead of treating every Bind failure as a simple wrong-password case.


## 73. How do you reduce LDAP account-lockout attacks?

- Rate-limit authentication attempts.
- Monitor failure rate by account and source.
- Use sensible thresholds and durations.
- Separate service-account and interactive-user policy.
- Protect applications from credential stuffing.
- Use MFA or Kerberos where appropriate.
- Avoid a policy where one source can lock thousands of accounts.

Lockout policy must balance brute-force protection against denial-of-service risk.


## 74. What are LDAP signing and channel binding in Active Directory?

LDAP signing protects message integrity. Channel binding ties authentication to the underlying TLS session through a Channel Binding Token.

Migration process:

1. Inventory unsigned and insecure clients.
2. Enable diagnostics.
3. Upgrade or reconfigure applications.
4. Validate signing and channel-binding support.
5. Enforce in phases.
6. Monitor domain-controller and application errors.
7. Retain rollback.

Do not enforce across all domain controllers without compatibility testing.


## 75. How do you harden LDAP network exposure?

- Allow only approved client networks.
- Separate replication and administrative access.
- Require TLS.
- Use mutual TLS where justified.
- Restrict local socket permissions.
- Monitor connection rate and failed Binds.
- Use redundant DNS and load balancers.
- Protect both IPv4 and IPv6 paths.
- Do not expose unrestricted directory ports to the Internet.


## 76. What should LDAP audit logging capture?

Capture authentication results, administrative Binds, user/group changes, ACL and schema changes, TLS and password-policy changes, replication administration, password resets without values, source address, connection security, result codes, and bulk exports.

Centralize logs, synchronize time, protect integrity, and redact secrets.


## 77. How do you secure application service accounts?

Use one account per application or trust boundary, least-privilege ACLs, TLS validation, secret management, rotation, no interactive login, connection/search limits, monitoring, and a documented owner.

Separate read and write identities when the risk justifies it. Do not share one broad Bind DN across unrelated applications.


## 78. Why must applications not read password hashes?

Hash access enables offline attacks and bypasses normal authentication controls. Deny hash reads, protect backups, prevent LDIF dumps in logs, and let applications verify credentials through Bind or a supported authentication service.


# 6. Replication, High Availability, Backup, and Disaster Recovery


## 79. Why is LDAP replication used?

Replication improves availability, read scalability, locality, and maintenance flexibility. It does not replace backup because deletion, corruption, and bad updates can replicate.

A design must define writable nodes, supplier/provider and consumer roles, conflict behavior, failure domains, replication security, monitoring, backup source, and recovery procedures.


## 80. Explain OpenLDAP syncrepl.

Syncrepl is a consumer-side replication engine. The consumer connects to a provider, performs an initial synchronization, stores a synchronization cookie, and continues in refresh-only or refresh-and-persist mode.

Key checks:

- Provider synchronization support
- Consumer `syncrepl` configuration
- Replication Bind identity and ACLs
- TLS trust
- Retry behavior
- Cookie and context state
- Schema consistency
- Log messages showing rejected changes


## 81. Compare refreshOnly and refreshAndPersist.

- **refreshOnly:** Consumer polls periodically. Recovery point depends on poll interval.
- **refreshAndPersist:** The connection remains open and receives changes continuously after refresh.

Refresh-and-persist reduces normal delay but needs stable long-lived connections. Refresh-only creates periodic load and longer lag.


## 82. What is delta-syncrepl?

Delta-syncrepl transfers changes from an access-log history rather than repeatedly comparing all data. It can reduce traffic and catch-up cost.

It requires correctly configured access logging, sufficient history retention, secure changelog protection, and consistent replication state. A consumer behind beyond retained history may require full refresh.


## 83. What is multi-provider replication?

Multiple providers accept writes and replicate among themselves.

Benefits:

- Write availability
- Maintenance flexibility
- Local write endpoint

Risks:

- Concurrent conflicts
- Duplicate naming collisions
- Network partitions
- Schema mismatch
- Application assumptions of immediate consistency
- More complex recovery

Multi-provider does not create synchronous, conflict-free consistency.


## 84. How does RHDS/389 DS replication work conceptually?

RHDS/389 DS supports supplier-initiated replication agreements, including single-supplier, multi-supplier, and cascading designs.

Important elements:

- Replica role and identifier
- Replication agreement
- Changelog
- Authentication and TLS
- Initialization
- Update-vector state
- Conflict and tombstone management
- Supported recovery procedures

Use the installed version's `dsconf replication` commands.


## 85. How do you monitor replication health?

Monitor:

- Last successful update
- Replication delay
- Backlog
- Agreement state
- Authentication/TLS errors
- Schema rejections
- Changelog size and trimming
- Time skew
- Conflict entries
- Disk capacity
- Repeated initialization

A successful TCP connection does not prove data convergence.


## 86. What causes replication lag?

Causes include network loss, TLS retries, slow disk, high write rate, bulk import, changelog contention, schema mismatch, consumer resource pressure, time skew, database problems, and outage beyond retained history.

Identify the bottleneck before changing thread counts or retention.


## 87. How do you troubleshoot schema-related replication failure?

1. Identify the rejected entry and attribute.
2. Compare schema OIDs and definitions.
3. Correct schema consistently.
4. Verify deployment order.
5. Replay or resume replication.
6. Reinitialize only if normal recovery is impossible.

Do not delete valid data merely to hide schema errors.


## 88. What is a replication conflict entry?

A conflict entry can appear when concurrent operations cannot be reconciled, such as duplicate Add or rename collisions.

Resolution:

- Identify authoritative business data.
- Merge required values.
- Repair references.
- Rename or delete conflict entries using supported procedures.
- Investigate the partition or application behavior that caused the conflict.

Conflict cleanup requires data-owner involvement.


## 89. Why is time synchronization important?

Time affects certificate validity, Kerberos, audit timelines, password expiry, lockout duration, replication diagnostics, and conflict analysis.

Use redundant NTP sources and alert on skew. Correct major time errors carefully.


## 90. How do you back up OpenLDAP?

Export configuration and data:

```bash
sudo slapcat -n 0 -l config-$(date +%F).ldif

sudo slapcat -b 'dc=example,dc=com'   -l data-$(date +%F).ldif
```

Also retain TLS material, schema, modules, service configuration, replication documentation, checksums, and software versions.

Backups can contain password hashes and personal data and must be strongly protected.


## 91. How do you back up RHDS or 389 Directory Server?

Use supported `dsctl` procedures. Depending on the objective, create a binary database backup or logical LDIF export.

Examples to investigate with installed help:

```bash
dsctl slapd-example db2bak
dsctl slapd-example db2ldif --help
dsctl slapd-example ldif2db --help
```

Test restore to an isolated instance. Do not assume a successful backup command proves recoverability.


## 92. Compare backup, export, and replica.

- **Backup:** Recoverable configuration and data copy.
- **Export:** Logical data representation, often LDIF.
- **Replica:** Live synchronized copy.

A replica receives bad changes. An export may omit server-specific state. A backup is unproven until restoration and application validation succeed.


## 93. How do you restore a directory safely?

1. Declare the recovery point and stop conflicting writes.
2. Verify checksum and version compatibility.
3. Preserve the failed state for evidence.
4. Restore configuration, schema, TLS, and data with supported tools.
5. Correct ownership and security labels.
6. Rebuild indexes if required.
7. Validate in isolation.
8. Test Bind, searches, ACLs, password operations, and data counts.
9. Rejoin or reinitialize replication safely.
10. Validate applications, RTO, and RPO.

Do not restore one supplier into a live multi-writer topology without understanding replication state.


## 94. What should a directory DR test validate?

Validate service startup, TLS, authentication, delegated ACLs, user/group searches, password changes, schema, replication, entry counts, application login, monitoring, audit logging, backup age, recovery duration, and external integrations such as Kerberos, DNS, PKI, and IAM provisioning.


# 7. Performance, Indexing, Monitoring, and Capacity


## 95. How do LDAP indexes improve performance?

An index reduces the candidate entries examined for a filter. Index types can include equality, presence, substring, approximate, and ordering depending on product.

Costs include disk usage, slower writes, longer import/reindex time, cache pressure, and larger backups. Index actual production filters rather than every attribute.


## 96. How do you identify an unindexed search?

Use access logs, vendor search statistics, and metrics.

Capture:

- Bind identity and source
- Base and scope
- Filter
- Requested attributes
- Candidate count
- Result count
- Elapsed time
- Server notes indicating unindexed evaluation

Repeated broad searches from one application often provide the highest-value optimization target.


## 97. Why can an indexed search still be slow?

Reasons include a huge candidate set, expensive ACL evaluation, large returned attributes, dynamic group computation, server-side sorting, cache misses, disk pressure, TLS/network latency, or too many small pages.

Indexing optimizes candidate selection but not every part of request processing.


## 98. What should be monitored on an LDAP server?

Monitor:

- Bind success/failure
- Search and update latency
- Operations per second
- Connection count and rejection
- Worker/thread saturation
- Database/cache behavior
- Disk latency and capacity
- CPU, memory, swap, and PSI
- File descriptors
- TLS failures
- Replication lag and backlog
- Changelog size
- Certificate expiry
- Backup age and restore-test status
- Unindexed searches
- Result-code distribution


## 99. How do you capacity-plan directory services?

Use historical data for entry growth, attribute size, group size, read/write ratio, peak Bind/search rate, search complexity, replication write amplification, backup/reindex duration, cache effectiveness, disk IOPS, TLS cost, and failure-domain headroom.

Plan to meet service levels during one replica failure and maintenance.


## 100. How do connection pools affect LDAP performance?

Pooling avoids repeated TCP, TLS, and Bind overhead. Poor pooling can cause:

- Connection storms
- File-descriptor exhaustion
- Load-balancer imbalance
- Idle connection accumulation
- Stale connections after failover
- Credential rotation problems

Set bounded pool size, health validation, idle timeout, retry policy, and failover behavior. Do not create one connection per authentication request.


## 101. How do large groups affect performance?

Large groups create expensive membership reads, updates, replication traffic, and application parsing. Nested-group expansion can be especially costly.

Options:

- Index membership attributes appropriately.
- Use member-of plugins carefully.
- Reduce repeated full-group reads.
- Use paged or ranged retrieval where supported.
- Cache with correct invalidation.
- Model authorization with application roles where appropriate.
- Monitor update latency and replication backlog.


## 102. What is cache tuning in a directory server?

Directory servers and the operating system cache database pages, entries, filters, and other structures depending on product.

Tune from observed hit rates, working set, memory pressure, and latency. Oversizing caches can cause swap or starve the OS page cache. Undersizing causes repeated disk I/O.

Measure before and after; do not copy cache values from another environment.


## 103. How do you benchmark LDAP responsibly?

Create representative data and workloads:

- Bind-heavy authentication
- Exact user lookup
- Group membership
- Broad administrative reports
- Writes and password changes
- Replication load

Measure latency percentiles, throughput, errors, CPU, memory, disk, cache, network, and replication lag.

Use a non-production environment or an approved controlled test. Avoid exposing real credentials or causing account lockouts.


## 104. What causes LDAP connection exhaustion?

Causes include connection leaks, unbounded pools, slow clients, long searches, idle connections, TLS handshake storms, low file-descriptor limits, firewall state exhaustion, and denial-of-service.

Remediation includes client fixes, connection timeouts, per-client limits, increased descriptors with capacity validation, rate limiting, and load distribution.


# 8. Active Directory and Application Integration


## 105. How is Active Directory different from a generic LDAP directory?

Active Directory implements LDAP but also includes Kerberos, DNS integration, domains, forests, security identifiers, group policy, Global Catalog, and Windows-specific controls.

Applications must understand domain naming contexts, login-name formats, objectGUID/objectSid, group scope and nesting, Global Catalog behavior, referrals, LDAP signing, channel binding, and AD password restrictions.

Do not assume OpenLDAP administrative commands or schema behavior apply to AD.


## 106. What Bind identities can applications use with Active Directory?

Common forms include:

```text
user@example.com
EXAMPLE\user
CN=Service Account,OU=Services,DC=example,DC=com
```

Support varies by library and authentication method. A UPN is often easier for applications because a DN can change after an OU move.

Use a dedicated, least-privilege service identity and protected transport.


## 107. What is the Active Directory Global Catalog?

The Global Catalog supports forest-wide search over a partial attribute set.

Common ports:

- 3268: Global Catalog
- 3269: Global Catalog over TLS

Use normal domain-controller LDAP endpoints for operations or attributes that the Global Catalog does not support. Test attribute availability before migration.


## 108. What are `objectGUID` and `objectSid`?

`objectGUID` is a binary globally unique identifier and is generally stable across rename and OU move. `objectSid` is the Windows security identifier used for authorization.

Applications should avoid using a mutable DN as the only permanent identity key. Binary attributes require correct library decoding.


## 109. How do nested groups affect AD LDAP queries?

Direct membership does not always represent transitive membership. AD supports implementation-specific recursive matching and authorization mechanisms.

Consider deep nesting, cross-domain groups, group scope, expensive recursive queries, cache staleness, and whether the application needs direct or transitive membership.


## 110. How do you discover Active Directory naming contexts?

Query root DSE:

```bash
ldapsearch -x -H ldaps://dc01.example.com   -b '' -s base   defaultNamingContext rootDomainNamingContext   configurationNamingContext schemaNamingContext
```

This avoids blindly hardcoding a base DN.


## 111. How should an application validate a user's password?

A common pattern is:

1. Bind with a restricted search account.
2. Search for exactly one user with a safely escaped filter.
3. Retrieve the user's DN or accepted login identity.
4. Open a separate TLS-protected connection.
5. Bind as that user.
6. Process account state and policy results.
7. Discard credentials immediately.
8. Perform application authorization separately.

Never log the supplied password.


## 112. What is the difference between authentication and group-based authorization?

Authentication proves identity. Authorization determines permitted actions.

A successful LDAP Bind does not prove the user belongs to the required application group. Define direct versus nested membership, disabled-account behavior, cache TTL, and fail-open or fail-closed behavior.


## 113. How do you design LDAP application failover?

Use multiple server URLs, DNS SRV, a load balancer, or library failover.

Requirements:

- Protocol-aware health checks
- Correct TLS names and trust
- Stale connection detection
- Bounded connect and operation timeouts
- Controlled retries
- Correct routing of writes
- Defined consistency and referral behavior

Avoid retry storms during a directory outage.


## 114. Why can `ldapsearch` succeed while the application fails?

Differences can include:

- Server and port
- StartTLS versus plaintext
- CA trust store
- Bind identity format
- Search base and scope
- Filter escaping
- Referral behavior
- Controls
- TLS version
- Container NetworkPolicy
- Proxy settings
- Stale connection pool
- Attribute-name mapping

Reproduce the application's exact request, not a simplified query.


## 115. How do Linux systems integrate with LDAP identities?

Common components include SSSD, NSS, PAM, Kerberos, and certificate-based authentication.

Design for identity lookup, authentication, offline cache, UID/GID uniqueness, group expansion, TLS trust distribution, failover, access rules, home directories, sudo rules, SSH keys, and cache invalidation.


## 116. How do you rotate LDAP application credentials without downtime?

Use controlled overlap:

- Create or enable a replacement service identity.
- Update secret management.
- Restart or reload one application instance.
- Validate Bind and searches.
- Roll through the fleet.
- Monitor failures.
- Disable the old credential after acceptance.

Do not rotate a shared Bind password until every consumer is identified.


# 9. Production Troubleshooting Scenarios

## 117. An application reports `Invalid credentials`, but the password is believed correct. What do you check?

Investigate in layers:

1. Confirm the exact Bind identity format.
2. Test the same endpoint and TLS mode with `ldapwhoami`.
3. Check lockout, expiry, disabled state, reset-required state, and login restrictions.
4. Verify which server processed the Bind.
5. Check time synchronization for Kerberos/SASL.
6. Review server security logs and vendor diagnostic details.
7. Confirm the application is not using a stale secret version.

Result code 49 is intentionally broad. Do not conclude “wrong password” without account-state evidence.

## 118. Bind succeeds but Search returns `insufficientAccessRights`. How do you troubleshoot?

Validate:

```bash
ldapwhoami -x -H ldaps://ldap01.example.com \
  -D 'uid=svc-app,ou=services,dc=example,dc=com' -W
```

Then check:

- Base entry access
- Search permission on attributes used in the filter
- Read permission on returned attributes
- ACL rule ordering
- Group membership used by ACLs
- Proxy authorization
- Referral target permissions

Start with a base-scope search and add scope, filter, and attributes gradually.

## 119. LDAP works on 389, but LDAPS on 636 fails. What do you inspect?

```bash
ss -lntp | grep :636

openssl s_client \
  -connect ldap01.example.com:636 \
  -servername ldap01.example.com \
  -showcerts </dev/null
```

Check listener configuration, certificate/key loading, file permissions, SELinux, firewall, load balancer, SAN, trust chain, and TLS versions. Enabling StartTLS does not automatically mean an LDAPS listener exists.

## 120. StartTLS fails with `Connect error` or `Protocol error`. What do you check?

Check that:

- The client sends StartTLS before Bind.
- The server advertises StartTLS support.
- A middlebox is not modifying the session.
- Certificate trust and hostname are correct.
- Client and server support compatible TLS versions.
- The connection is not already protected by TLS.
- The application is not sending credentials before upgrade.

Use `-ZZ` to require StartTLS and fail closed.

## 121. A search returns no users after migration. What is your method?

Compare old and new:

- Base DN
- Scope
- Filter
- Object classes
- Attribute names
- Filter escaping
- Referral policy
- Paged-results control
- Bind identity and ACL
- Schema
- Global Catalog versus domain LDAP

Test the base entry first, then a known user DN, then the complete application filter.

## 122. CPU is high and logs show broad subtree searches. What do you do?

1. Identify source, Bind DN, base, scope, filter, requested attributes, and frequency.
2. Rate-limit or temporarily disable the abusive client if required.
3. Narrow the application query.
4. Add evidence-based indexes.
5. Apply account-specific time and size limits.
6. Reindex in a controlled window.
7. Monitor write latency, disk growth, and replication.
8. Add recurrence alerts.

Do not add every index or globally raise limits.

## 123. One replica returns stale data. How do you isolate the cause?

1. Confirm which replica the client reached.
2. Compare a known entry and operational metadata.
3. Inspect replication session/agreement state.
4. Measure lag or backlog.
5. Check schema rejection.
6. Validate disk, CPU, network, and clock.
7. Check changelog retention and synchronization state.
8. Decide whether normal catch-up or reinitialization is required.

Do not manually update a read-only consumer.

## 124. Replication stopped after certificate rotation. What do you check?

Check the new certificate SAN, issuer chain, peer trust store, replication hostname, client-certificate mapping, private-key permissions, reload/restart status, TLS policy, and replication authentication method.

Test TLS independently and then authenticate with the replication identity through a secure procedure.

## 125. The directory filesystem is nearly full. What is the safe response?

- Stop nonessential bulk operations.
- Measure backend, changelog, logs, backups, and temporary files.
- Check inode usage.
- Protect a current backup.
- Expand storage or safely remove old nonessential files.
- Correct log rotation and changelog retention.
- Check MDB map-size separately.
- Validate database and replication after remediation.

Never delete backend database files blindly.

## 126. OpenLDAP reports MDB map full. How do you respond?

Confirm the current map size, database growth, and filesystem capacity. Back up configuration and data, then increase the supported MDB map-size setting through `cn=config`.

Validate the correct database DN, any restart requirement, write recovery, and future growth alerts. MDB map-size and filesystem free space are different limits.

## 127. An LDIF import fails with `objectClassViolation`. What do you inspect?

Check:

- Required `MUST` attributes
- Structural object-class chain
- Whether the attribute is permitted
- Target schema
- Naming attribute in the entry
- Parent entry existence
- Vendor-specific schema differences
- Import order

Test one failing record before repeating the full import.

## 128. An LDIF import fails with `invalidAttributeSyntax`. What do you inspect?

Check DN escaping, generalized time, integer/boolean syntax, binary base64 representation, UTF-8 validity, empty values, line folding, and hidden carriage returns.

Useful commands:

```bash
file import.ldif
sed -n '1,120l' import.ldif
```

## 129. Users are intermittently locked out after a new application deployment. What do you investigate?

Look for stale credentials in connection pools, repeated retries, many application replicas, health checks using real users, failover loops, incomplete credential rotation, cached old passwords, or authentication against multiple domains.

Contain the source before repeatedly unlocking users.

## 130. LDAP fails only through a load balancer. What do you test?

Compare direct and virtual endpoints:

- TLS SAN and SNI
- StartTLS pass-through
- Idle timeout
- Source NAT and ACL behavior
- Backend health checks
- Session persistence
- Connection resets during large searches
- Mixed backend certificates or versions
- Proxy protocol expectations

Long-lived LDAP connection pools can remain pinned to one unhealthy backend.

## 131. Kerberos/GSSAPI Bind fails while simple Bind works. What do you check?

Validate:

```bash
klist
```

Then inspect the service principal, DNS forward/reverse resolution, realm mapping, keytab, encryption types, clock skew, advertised SASL mechanisms, identity mapping, channel binding, and ACLs.

Simple Bind success proves connectivity, not Kerberos integration.

## 132. An LDAP update succeeded, but the application still sees old data. Why?

Possible causes:

- Reads go to another asynchronous replica.
- Application or proxy cache.
- Negative cache.
- Global Catalog partial attribute set.
- Search filter excludes the modified entry.
- The write reached an isolated supplier.
- External IAM synchronization is incomplete.
- Application uses a stale connection or result cache.

Trace the write endpoint, read endpoint, replication state, and cache TTL.

## 133. How do you respond to suspected LDAP credential compromise?

1. Activate incident response.
2. Identify the Bind DN, privileges, source addresses, and time window.
3. Rotate or disable the credential with a controlled application plan.
4. Review reads, modifications, group changes, resets, ACL changes, and bulk exports.
5. Preserve logs and backups.
6. Validate all replicas and configuration.
7. Rotate downstream credentials exposed through directory data.
8. Reduce permissions and correct secret handling.
9. Document scope and notification requirements.

# 10. Rapid-Fire Command Questions

## 134. What does `ldapwhoami` prove?

It proves that the server accepted a Bind and reports the effective authorization identity.

```bash
ldapwhoami -x -ZZ -H ldap://ldap01.example.com \
  -D 'uid=svc-app,ou=services,dc=example,dc=com' -W
```

It does not prove Search, read, or write authorization.

## 135. How do you query only the root DSE?

```bash
ldapsearch -x -H ldaps://ldap01.example.com \
  -b '' -s base '(objectClass=*)' '*' '+'
```

Use it to discover naming contexts, controls, extensions, LDAP versions, and SASL mechanisms.

## 136. How do you test whether a base DN exists?

```bash
ldapsearch -x -H ldaps://ldap01.example.com \
  -b 'ou=people,dc=example,dc=com' \
  -s base '(objectClass=*)' dn
```

Success with one entry proves the base exists and is visible to the identity.

## 137. How do you request only selected attributes?

```bash
ldapsearch -x -H ldaps://ldap01.example.com \
  -b 'ou=people,dc=example,dc=com' \
  '(uid=rakesh)' dn uid cn mail
```

Request only what the application needs to reduce data exposure and response size.

## 138. How do you suppress normal attributes or request no attributes?

Use LDAP special attribute selectors supported by the client:

- `*` for user attributes
- `+` for operational attributes
- `1.1` for no attributes

Example:

```bash
ldapsearch -x -H ldaps://ldap01.example.com \
  -b 'dc=example,dc=com' '(uid=rakesh)' 1.1
```

## 139. How do you compare an attribute value?

```bash
ldapcompare -x -H ldaps://ldap01.example.com \
  -D 'uid=svc-app,ou=services,dc=example,dc=com' -W \
  'uid=rakesh,ou=people,dc=example,dc=com' \
  'accountStatus:active'
```

The result distinguishes compare true, compare false, and operation errors.

## 140. How do you delete an entry safely?

First export and confirm the exact DN:

```bash
ldapsearch -x -H ldaps://ldap01.example.com \
  -b 'uid=test01,ou=people,dc=example,dc=com' \
  -s base '(objectClass=*)' '*' '+' > test01-backup.ldif
```

Then delete with a delegated identity:

```bash
ldapdelete -x -ZZ -H ldap://ldap01.example.com \
  -D 'uid=delegated-admin,ou=services,dc=example,dc=com' -W \
  'uid=test01,ou=people,dc=example,dc=com'
```

Review references and replication impact first.

## 141. How do you inspect OpenLDAP configuration locally?

```bash
ldapsearch -Q -Y EXTERNAL -H ldapi:/// \
  -b 'cn=config' '(objectClass=*)' dn
```

Local root/SASL EXTERNAL access is powerful and must be audited.

## 142. How do you validate configuration before restart?

For OpenLDAP:

```bash
sudo slaptest -u
```

For RHDS/389 DS:

```bash
sudo dsctl slapd-example healthcheck
```

Also perform a protocol-level health check after restart.

## 143. How do you capture LDAP network traffic safely?

Use packet capture only with authorization:

```bash
sudo tcpdump -ni any host ldap01.example.com and port 389
```

Unencrypted LDAP can expose credentials and personal data. Protect capture files, minimize duration, and prefer metadata or TLS-aware server logs.

# 11. Corporate Design and Governance Questions

## 144. How would you design an enterprise LDAP service?

Include:

- At least two servers across failure domains
- Clear write topology
- TLS and strong identity
- Least-privilege ACLs
- Dedicated application accounts
- Replication monitoring
- Backup and restore drills
- Index governance
- Capacity and growth monitoring
- Certificate and password rotation
- Audit logging
- Patch and upgrade procedures
- Defined RTO/RPO
- Break-glass access
- Application onboarding standards

## 145. How do you govern schema changes?

Require an owner, business purpose, unique OIDs, compatibility review, non-production testing, deployment order, backup, replica validation, application testing, rollback, and documentation.

Schema is an enterprise API contract and should not be changed informally.

## 146. How do you govern LDAP indexes?

Require access-log evidence, expected filter patterns, estimated disk and write cost, maintenance window, reindex plan, rollback, before/after latency, and post-change monitoring.

Remove obsolete indexes only after confirming no clients use them.

## 147. What is a good LDAP application onboarding checklist?

Collect:

- Application owner
- Environments
- Required users/groups
- Base DN and filter
- Attributes
- Read/write needs
- Expected request rate
- TLS and trust requirements
- Failover behavior
- Service-account owner
- Rotation method
- Timeout and pooling configuration
- Audit requirements
- Data classification
- Decommission plan

## 148. How should LDAP upgrades be performed?

1. Review supported versions and release notes.
2. Verify schema, backend, module, TLS, and replication compatibility.
3. Back up configuration and data.
4. Test restore.
5. Upgrade one replica or lab clone.
6. Validate Bind, search, update, TLS, replication, and applications.
7. Roll through remaining nodes.
8. Maintain rollback criteria.
9. Monitor latency, result codes, and replication.

## 149. What is your LDAP incident evidence package?

Capture:

- Incident timeline and affected applications
- Exact endpoints and replicas
- Recent changes
- Service and journal logs
- Access, error, security, and audit logs
- Replication state
- Root DSE and configuration metadata
- TLS chain and expiry
- CPU, memory, disk, network, and file descriptors
- Slow/unindexed search details
- Commands and changes made
- Recovery validation
- Redaction record for credentials and personal data

---

# 12. L3 Interview Answer Framework

For every production scenario:

## Confirm impact

- Which users, applications, servers, and sites?
- Is the failure total, partial, or intermittent?
- When did it begin?
- What changed?
- Is authentication, authorization, data integrity, or availability affected?

## Stabilize

- Stop harmful bulk jobs.
- Preserve logs and metrics.
- Remove a bad replica from the load balancer.
- Rate-limit an abusive client.
- Protect backups and replication state.
- Avoid random restarts or reinitialization.

## Isolate the failing layer

```text
Application
  -> DNS / load balancer
  -> TCP
  -> TLS
  -> Bind
  -> Authorization / ACL
  -> Search or update
  -> Backend database
  -> Replication
  -> Application interpretation
```

## Correct safely

- Make the smallest reversible change.
- Change one replica at a time.
- Preserve rollback LDIF and configuration.
- Avoid disabling certificate validation or ACLs.
- Do not force replication recovery without understanding state.

## Validate

- Root DSE
- TLS
- Bind
- Exact application search
- Update if required
- Replication convergence
- Application login
- Monitoring and audit recovery

# 13. Common Weak Interview Answers to Avoid

- “LDAP is a database on port 389.”
- “Use port 636 and it is automatically secure.”
- “Disable certificate verification to fix TLS.”
- “Invalid credentials always means the password is wrong.”
- “A replica is a backup.”
- “Index every attribute.”
- “Restart slapd first.”
- “Use Directory Manager for the application.”
- “Increase all search limits.”
- “Reinitialize replication whenever lag appears.”
- “DNs can be parsed by splitting on commas.”
- “Base64 password data is encrypted.”
- “Active Directory is exactly the same as OpenLDAP.”

# 14. Official References

- LDAP Technical Specification Road Map — RFC 4510: <https://www.rfc-editor.org/rfc/rfc4510>
- LDAP Protocol — RFC 4511: <https://www.rfc-editor.org/rfc/rfc4511>
- LDAP Directory Information Models — RFC 4512: <https://www.rfc-editor.org/rfc/rfc4512>
- LDAP Authentication and Security — RFC 4513: <https://www.rfc-editor.org/rfc/rfc4513>
- Distinguished Name String Representation — RFC 4514: <https://www.rfc-editor.org/rfc/rfc4514>
- Search Filter String Representation — RFC 4515: <https://www.rfc-editor.org/rfc/rfc4515>
- LDAP URL — RFC 4516: <https://www.rfc-editor.org/rfc/rfc4516>
- OpenLDAP 2.6 Administrator's Guide: <https://www.openldap.org/doc/admin26/>
- Red Hat Directory Server 13 Documentation: <https://docs.redhat.com/en/documentation/red_hat_directory_server/13>
- Microsoft LDAP Signing Guidance: <https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/ldap-signing>

---

**End of guide**
