# Red Hat Ceph Storage Corporate L3 Interview Questions and Detailed Answers

> **Version baseline (August 2026):** Red Hat Ceph Storage (RHCS) 9 is the current major release family, and Red Hat publishes current RHCS 9.1 documentation primarily as PDF publications. The commands and concepts in this guide focus on modern containerized, `cephadm`-managed clusters. Always validate exact syntax, supported hardware, upgrade paths, and feature compatibility against the documentation and support matrix for your installed RHCS minor release.

**Target audience:** Senior Linux, storage, cloud, OpenStack, OpenShift, SRE, and Ceph administrators with approximately 8–12+ years of enterprise experience.

**Format:** Each question includes an interview-ready answer, technical deep dive, operational commands, L3 validation points, common mistakes, and a closing statement.


---

# Fundamentals and Architecture

## Q1. What is Red Hat Ceph Storage, and where does it fit in an enterprise architecture?

### Interview answer

Red Hat Ceph Storage is an enterprise-hardened, software-defined, distributed storage platform based on Ceph. It stores data as RADOS objects and exposes block storage through RBD, shared file storage through CephFS, and object storage through the Ceph Object Gateway. Its main enterprise value is horizontal scale, failure-domain-aware resilience, commodity-hardware flexibility, and a common storage backend for cloud, virtualization, analytics, backup, and large unstructured-data workloads.

### Deep technical explanation

- Ceph separates logical storage services from physical disks. Applications do not need to know which disk contains an object; clients and OSDs calculate placement with CRUSH.
- The core storage cluster normally consists of MON, MGR, and OSD daemons. Additional services such as MDS, RGW, NFS-Ganesha, iSCSI gateways, monitoring components, and mirroring daemons are deployed according to the access protocol and availability design.
- RBD is usually selected for virtual machines, OpenStack volumes, databases, and container persistent volumes. CephFS is selected for shared POSIX namespaces. RGW is selected for S3-compatible object workloads.
- An L3 engineer must design for failure domains, usable capacity, recovery bandwidth, network separation, operational automation, security, observability, lifecycle management, and application-specific latency or throughput objectives.

### Useful commands

```bash
ceph -s
ceph versions
ceph orch ls
ceph osd tree
ceph df
```


### L3 validation points

- Explain the workload before recommending RBD, CephFS, or RGW.
- Discuss recovery and failure behavior, not only normal-state capacity.
- Differentiate community Ceph features from features supported by the installed RHCS release.

### Common mistake

Calling Ceph only an object-storage product. RADOS is the internal object layer, but enterprise consumers commonly use block, file, and object interfaces.

### Strong L3 closing statement

> I treat Ceph as a distributed storage platform, not a collection of disks. The design starts with workload SLOs and failure domains, then maps those requirements to pools, CRUSH rules, interfaces, and operational controls.


## Q2. Explain the role of MON, MGR, OSD, MDS, and RGW daemons.

### Interview answer

MON daemons maintain authoritative cluster maps and form quorum. MGR daemons aggregate runtime statistics, serve many management queries, host modules such as dashboard and orchestrator, and provide management APIs. OSD daemons store RADOS objects and perform replication or erasure coding, peering, recovery, backfill, scrubbing, and heartbeats. MDS daemons manage CephFS metadata. RGW daemons translate S3- or Swift-compatible requests into RADOS operations.

### Deep technical explanation

- MON is a consistency and coordination service, not the data path for normal client I/O. Losing quorum prevents map updates and can block clients that need current maps.
- MGR improves operational scalability by taking many statistics and management workloads away from MONs. At least one active MGR and one or more standbys should be available.
- Each OSD generally represents one storage device or one logical BlueStore instance. OSDs are both data servers and distributed-storage control participants.
- MDS is required only for CephFS. File data still resides in OSD-backed pools; MDS manages directory hierarchy, inode metadata, capabilities, and metadata journaling.
- RGW is a stateless-ish gateway tier from the client perspective, but bucket indexes, users, metadata, and objects are persisted in Ceph pools. Load balancing and multisite design are separate concerns.

### Useful commands

```bash
ceph quorum_status --format json-pretty
ceph mgr dump
ceph osd stat
ceph fs status
ceph orch ps --daemon-type rgw
```


### L3 validation points

- State which daemons are in the client data path.
- Describe quorum and active/standby behavior.
- Explain why scaling RGW or MDS is different from adding OSD capacity.

### Common mistake

Saying the Monitor stores user data or that all client I/O passes through the Monitor.

### Strong L3 closing statement

> MON and MGR provide control and management, OSDs provide the distributed data plane, and MDS or RGW add protocol-specific services above RADOS.


## Q3. How does a Ceph client locate data without a central metadata lookup service?

### Interview answer

The client obtains the cluster maps from the monitors, hashes an object into a placement group, and applies the pool’s CRUSH rule to determine the acting OSD set. The client then communicates directly with the primary OSD for that placement group. This distributed calculation avoids a central data-location database and enables horizontal scalability.

### Deep technical explanation

- The mapping path is conceptually: object name plus namespace → object hash → placement group → CRUSH rule → acting set → primary OSD.
- The primary OSD coordinates writes to secondary OSDs for a replicated pool or to the erasure-coded shard set. The client receives acknowledgement according to the write-completion semantics of the operation.
- Cluster maps are versioned by epoch. When topology or OSD state changes, clients receive newer maps and recalculate placement.
- Placement groups provide a manageable indirection layer between billions of objects and a changing OSD population.

### Useful commands

```bash
ceph osd map <pool-name> <object-name>
ceph osd getmap -o /tmp/osdmap
ceph osd crush rule dump
ceph pg map <pgid>
```


### L3 validation points

- Explain the difference between object-to-PG mapping and PG-to-OSD mapping.
- Include the role of the primary OSD.
- Mention map epochs during failures or topology changes.

### Common mistake

Describing CRUSH as a database or assuming the client queries every OSD to find an object.

### Strong L3 closing statement

> Ceph scales because placement is calculated consistently by clients and daemons from shared maps rather than retrieved from a centralized lookup table.


## Q4. What are RADOS, librados, RBD, CephFS, and RGW?

### Interview answer

RADOS is the reliable autonomic distributed object store formed by the MON, MGR, and OSD services. `librados` is the native client library used to access RADOS objects. RBD is a distributed block-device layer implemented above RADOS. CephFS is a POSIX-style distributed file system using RADOS data and metadata pools plus MDS daemons. RGW is an HTTP gateway that provides S3- and Swift-compatible object APIs above RADOS.

### Deep technical explanation

- RADOS provides object operations, replication or erasure coding, recovery, self-healing, and placement.
- RBD stripes block images over RADOS objects and supports features such as snapshots, clones, exclusive lock, object map, fast-diff, deep flatten, and mirroring.
- CephFS clients can use the kernel client or FUSE. MDS handles metadata while clients generally perform file data I/O directly against OSDs.
- RGW handles authentication, bucket semantics, multipart uploads, lifecycle rules, quotas, and multisite replication using dedicated pools and metadata structures.

### Useful commands

```bash
rados lspools
rbd pool ls
ceph fs ls
ceph orch ls --service_type rgw
```


### L3 validation points

- Match interfaces to workloads.
- Explain that all interfaces ultimately consume RADOS pools.
- Discuss protocol-specific scaling bottlenecks.

### Common mistake

Equating RADOS objects with S3 objects. S3 objects are represented by RGW structures over RADOS and may not map one-to-one in a simplistic way.

### Strong L3 closing statement

> I choose the access layer based on application semantics, while protecting the common RADOS backend through appropriate pool and CRUSH design.


## Q5. How does Ceph provide high availability without RAID controllers?

### Interview answer

Ceph provides software-defined redundancy at the object or shard level across independent OSDs and failure domains. CRUSH places replicas or erasure-coded shards across hosts, racks, rooms, or sites according to policy. OSD heartbeats, peering, recovery, scrubbing, and checksums allow the cluster to detect failures and restore the configured redundancy level.

### Deep technical explanation

- A replicated pool commonly stores multiple full copies. An erasure-coded pool stores data and coding shards, reducing capacity overhead at the cost of additional CPU, I/O amplification, and recovery complexity.
- Failure domains must match physical risk. A three-copy pool with `host` failure domain does not protect against rack loss if all replicas can land in the same rack.
- Hardware RAID beneath OSDs can obscure disk-level failures, complicate replacement, and reduce Ceph’s ability to distribute risk. Enterprise designs normally expose individual drives to BlueStore, subject to the supported hardware design.
- MON quorum and redundant MGR, network, power, and gateway layers are also required; data redundancy alone does not make the entire service highly available.

### Useful commands

```bash
ceph osd pool get <pool> size
ceph osd pool get <pool> min_size
ceph osd crush rule dump <rule-name>
ceph osd tree
```


### L3 validation points

- Identify the configured failure domain.
- Explain `size` versus `min_size`.
- Include control-plane and client-access HA.

### Common mistake

Saying that three replicas automatically protect against any three failures. Placement topology and correlated failures determine actual resilience.

### Strong L3 closing statement

> Availability is the result of end-to-end failure-domain design: data placement, quorum, networks, gateways, power, and tested operational procedures.


## Q6. Explain the difference between availability, durability, consistency, and performance in Ceph.

### Interview answer

Availability is the ability to continue serving I/O. Durability is the probability that acknowledged data is not lost. Consistency is the correctness and ordering model seen by clients. Performance covers latency, throughput, and IOPS. Ceph settings can trade these properties, so an L3 engineer must avoid optimizing one metric in a way that violates the workload’s durability or availability objectives.

### Deep technical explanation

- Reducing pool `size` or `min_size` can increase apparent availability or usable capacity but may expose acknowledged writes to unacceptable loss risk.
- Recovery settings influence how quickly redundancy is restored, but aggressive recovery can raise foreground latency.
- Erasure coding improves usable capacity efficiency but can increase small-write cost and recovery CPU/network demand.
- Client cache settings can reduce latency but change failure behavior. For example, RBD caching and application flush semantics must be understood together.
- Consistency incidents can arise from incomplete application quiescing, unsupported snapshot assumptions, clock issues at protocol layers, or bypassing safe recovery procedures.

### Useful commands

```bash
ceph health detail
ceph osd perf
ceph tell osd.* bench
ceph config dump
```


### L3 validation points

- Use workload SLOs rather than generic 'best performance' tuning.
- Explain failure consequences of proposed changes.
- Require measurable before-and-after evidence.

### Common mistake

Treating `HEALTH_OK` as proof that application latency, durability objectives, and recovery readiness are all acceptable.

### Strong L3 closing statement

> I make tuning decisions against explicit SLOs and failure assumptions; a faster cluster is not better if it weakens the required data-safety model.


## Q7. What is the recommended network design for a production Ceph cluster?

### Interview answer

A production design provides redundant, low-latency networking with sufficient bandwidth for client traffic, replication, recovery, backfill, heartbeats, and management. Many deployments separate public/client traffic from cluster/replication traffic, but the exact design depends on workload, host density, link speed, and supported architecture. Bonding, switch redundancy, MTU consistency, routing symmetry, and capacity for a full failure-domain recovery are critical.

### Deep technical explanation

- The public network carries client-to-cluster and daemon management traffic. The cluster network, when configured, carries OSD replication, backfill, and heartbeat traffic.
- Network capacity must be calculated during degraded recovery, not only steady-state application traffic. A failed host can shift both client and recovery load to remaining links.
- Jumbo frames help only when configured end to end. An MTU mismatch can create intermittent timeouts, packet fragmentation, or silent performance degradation.
- RSS/RPS, IRQ distribution, NIC ring sizes, offloads, NUMA placement, and switch buffers can matter at high throughput, but changes must be evidence-driven.
- Time synchronization, DNS or hostname consistency, firewall rules, and container-network behavior are operational dependencies.

### Useful commands

```bash
ceph config get mon public_network
ceph config get osd cluster_network
ip -s link
ethtool -S <interface>
ss -s
```


### L3 validation points

- Validate throughput under recovery.
- Check packet drops and retransmissions on hosts and switches.
- Verify consistent MTU and redundant paths.

### Common mistake

Assuming a nominal 25/100 Gb link guarantees usable bandwidth without checking PCIe lanes, NUMA, bonding mode, switch oversubscription, or recovery concurrency.

### Strong L3 closing statement

> I size Ceph networking for the worst credible degraded state and validate the complete path, not just the NIC link speed.


## Q8. What should be included in a Ceph production readiness review?

### Interview answer

A production readiness review should validate supported versions and hardware, daemon placement, failure domains, capacity and growth, performance baselines, network resiliency, security, monitoring, backup and disaster recovery, upgrade procedures, operational ownership, and tested failure scenarios. The review must produce evidence and rollback plans rather than relying on configuration screenshots alone.

### Deep technical explanation

- Confirm Red Hat support matrices, lifecycle dates, container images, RHEL versions, firmware, drive models, and interoperability with clients such as OpenStack or OpenShift.
- Model raw, usable, reserved, and failure-state capacity. Include full-ratio thresholds, BlueStore overhead, EC or replica overhead, and one or more failed hosts.
- Run workload-representative tests and record latency percentiles, IOPS, throughput, CPU, memory, network, OSD commit/apply latency, and recovery impact.
- Test OSD, host, MON, MGR, network-path, gateway, and site failures. Verify alerting, escalation, evidence collection, and recovery time.
- Review cephx least privilege, encryption in transit and at rest, secrets handling, audit logs, administrative access, and change controls.

### Useful commands

```bash
ceph report > ceph-report.json
ceph health detail
ceph df detail
ceph orch host ls --detail
ceph config dump > ceph-config.txt
```


### L3 validation points

- Evidence must include degraded-state testing.
- Application owners must sign off on SLOs.
- Runbooks must specify safe commands and abort criteria.

### Common mistake

Approving production based only on `ceph -s` showing `HEALTH_OK`.

### Strong L3 closing statement

> My readiness gate proves that the cluster can meet workload SLOs, survive defined failures, and be operated safely by the on-call team.



---

# Cluster Maps, CRUSH, Placement Groups, and Pools

## Q1. What cluster maps does Ceph maintain, and why are epochs important?

### Interview answer

Ceph maintains versioned maps such as the monitor map, OSD map, PG map, MDS map, and CRUSH map. Each update advances an epoch. Clients and daemons use these maps to agree on membership, state, placement, and service roles. Epochs allow participants to identify stale information and converge on a consistent cluster view.

### Deep technical explanation

- The OSD map contains OSD membership and state, pool configuration, and related placement information.
- The CRUSH map represents devices, buckets, hierarchy, weights, device classes, and placement rules.
- The monitor map identifies monitor members and addresses. The MDS map tracks CephFS ranks and states.
- During failures, map churn can increase. Excessive flapping causes repeated peering and remapping, so the root cause must be corrected instead of merely tuning timeouts.

### Useful commands

```bash
ceph osd dump | head -50
ceph mon dump
ceph osd crush dump
ceph fs dump
ceph report
```


### L3 validation points

- Correlate incident timestamps with map epoch changes.
- Look for OSD flapping before changing recovery settings.
- Confirm clients can reach all current daemon addresses.

### Common mistake

Treating an epoch as time in seconds. It is a monotonically increasing map version.

### Strong L3 closing statement

> Map epochs are the audit trail of cluster topology and state changes; they are essential when reconstructing a peering or connectivity incident.


## Q2. Explain CRUSH hierarchy, buckets, rules, device classes, and weights.

### Interview answer

CRUSH models physical topology as devices under buckets such as hosts, racks, rows, rooms, and roots. Rules define how replicas or shards are selected across that hierarchy. Device classes such as HDD, SSD, or NVMe allow rules to target media categories. CRUSH weights represent relative capacity and influence data distribution; reweight controls can temporarily adjust effective placement.

### Deep technical explanation

- A rule typically selects a root, chooses failure-domain buckets, and then chooses OSD leaves. The failure domain must match the required resilience.
- CRUSH device weight is normally capacity-based. `ceph osd reweight` is an override in the OSD map and should not become a permanent substitute for correcting CRUSH weights or using the balancer.
- Class-based roots and rules help separate fast and capacity tiers without manually maintaining one tree per device type.
- Moving a bucket or changing a rule can trigger large data movement. Use test maps, change windows, and movement estimates.

### Useful commands

```bash
ceph osd crush tree
ceph osd crush rule ls
ceph osd crush rule dump <rule>
ceph osd df tree
crushtool -d <crushmap> -o <text-file>
```


### L3 validation points

- Validate the physical topology against the CRUSH hierarchy.
- Estimate data movement before applying rule or weight changes.
- Do not mix device types in a pool unless intentionally designed.

### Common mistake

Using CRUSH weight and OSD reweight interchangeably without understanding their different locations and operational effects.

### Strong L3 closing statement

> I use CRUSH to encode the organization’s physical failure model, not just to spread data evenly.


## Q3. What is a placement group, and why does Ceph use PGs?

### Interview answer

A placement group is a logical aggregation of objects used as an indirection layer between objects and OSDs. Objects hash into PGs, and CRUSH maps each PG to an acting OSD set. PGs make placement, peering, recovery, and balancing manageable without tracking every object in the monitor maps.

### Deep technical explanation

- Too few PGs can produce uneven distribution and hot OSDs. Too many PGs increase memory, peering, and management overhead.
- The PG autoscaler estimates appropriate PG counts based on pool usage, target size, bias, and cluster topology.
- PG count changes can split or merge PGs and create background work. Schedule and monitor such changes.
- PG state is one of the most important troubleshooting signals because it combines placement, replica, peering, and recovery status.

### Useful commands

```bash
ceph pg stat
ceph pg dump pgs_brief
ceph osd pool autoscale-status
ceph osd pool get <pool> pg_autoscale_mode
ceph pg ls-by-pool <pool>
```


### L3 validation points

- Review autoscaler recommendations and pool target ratios.
- Investigate persistent non-active or non-clean PGs.
- Relate PG count to OSD count and pool size.

### Common mistake

Using an old fixed PG-per-OSD rule without considering the autoscaler, pool usage, and current supported guidance.

### Strong L3 closing statement

> PG design balances distribution quality against daemon overhead; I manage it as a dynamic capacity parameter, not a one-time guess.


## Q4. Explain common PG states: active, clean, degraded, undersized, peering, stale, inactive, backfilling, and remapped.

### Interview answer

`active` means the PG can serve I/O, and `clean` means all expected replicas or shards are present and synchronized. `degraded` means one or more objects lack the desired redundancy. `undersized` means the acting set has fewer members than the pool size. `peering` means OSDs are establishing an authoritative history. `stale` indicates monitors have not received recent PG information. `inactive` means the PG cannot serve I/O. `backfilling` and `remapped` indicate data movement toward the intended placement.

### Deep technical explanation

- A PG can be active but degraded, so application I/O may continue while durability is reduced.
- Peering waits often result from missing OSD histories, network partitions, incomplete maps, or blocked recovery dependencies.
- Stale PGs usually point to missing primary OSD reporting or monitor-to-OSD communication issues.
- Backfill can be blocked by full thresholds, unavailable target OSDs, or configuration limits.
- Interpret the complete state string and the associated health detail rather than focusing on a single word.

### Useful commands

```bash
ceph health detail
ceph pg <pgid> query
ceph pg map <pgid>
ceph osd blocked-by
ceph tell <pgid> query
```


### L3 validation points

- Identify whether client I/O is blocked.
- Determine the authoritative OSD history before destructive action.
- Check full-ratio and network conditions.

### Common mistake

Using `ceph pg repair` as a generic fix for peering or inactive PGs.

### Strong L3 closing statement

> I treat PG state as a symptom map and trace it through acting sets, OSD histories, network reachability, and capacity constraints before intervening.


## Q5. How do replicated pools differ from erasure-coded pools?

### Interview answer

Replicated pools store complete object copies and generally provide simpler recovery and better small-random-write behavior at higher capacity overhead. Erasure-coded pools split data into `k` data shards and `m` coding shards, improving usable capacity efficiency but increasing CPU, network, and small-write amplification. The choice is workload- and failure-model-specific.

### Deep technical explanation

- A 3x replicated pool has roughly one-third raw-to-logical efficiency before reserve and overhead. A `k=8, m=3` EC profile has theoretical data efficiency of 8/11 before operational reserve.
- EC recovery may read and reconstruct multiple shards. This can increase degraded-read latency and recovery traffic.
- RBD and CephFS metadata are normally placed on replicated pools. Data pools may use EC where supported and appropriate.
- EC profile and CRUSH failure domain must be selected before large-scale data placement; changing EC layout generally requires data migration to a new pool.

### Useful commands

```bash
ceph osd erasure-code-profile ls
ceph osd erasure-code-profile get <profile>
ceph osd pool get <pool> erasure_code_profile
ceph osd pool get <pool> size
```


### L3 validation points

- Calculate failure tolerance and usable capacity.
- Benchmark the actual I/O pattern.
- Confirm client and feature support for EC overwrite behavior.

### Common mistake

Selecting erasure coding only because it saves capacity, without testing latency, CPU, recovery, and small-object overhead.

### Strong L3 closing statement

> Replicated and EC pools are different service classes; I choose them from SLOs, data size, write pattern, recovery objectives, and cost.


## Q6. What are `size` and `min_size`, and how would you configure them safely?

### Interview answer

For a replicated pool, `size` is the desired number of replicas. `min_size` is the minimum number of available replicas required for writes. For an EC pool, comparable values represent total shards and the minimum needed for safe I/O according to the profile. These values must be selected to preserve the organization’s data-loss tolerance during failures.

### Deep technical explanation

- With a three-copy replicated pool, a common design keeps three replicas and requires enough healthy copies to avoid acknowledging writes in an unsafe single-copy state.
- Changing `min_size` during an incident can restore writes but may significantly increase data-loss risk. It requires explicit business approval and a documented recovery plan.
- The CRUSH failure domain must make the replica count meaningful. Three replicas on one host do not protect against host loss.
- Capacity and maintenance planning must ensure the cluster can satisfy `size` after expected failures.

### Useful commands

```bash
ceph osd pool get <pool> size
ceph osd pool get <pool> min_size
ceph osd pool set <pool> size <n>
ceph osd pool set <pool> min_size <n>
```


### L3 validation points

- Require risk approval before emergency changes.
- Restore the normal policy immediately after recovery.
- Verify object redundancy and PG cleanliness.

### Common mistake

Lowering `min_size` as the first response to blocked I/O without understanding why replicas are unavailable.

### Strong L3 closing statement

> I never treat `min_size` as a performance knob; it is a durability and write-availability policy.


## Q7. How do the PG autoscaler and balancer differ?

### Interview answer

The PG autoscaler changes or recommends the number of placement groups allocated to pools. The balancer improves how existing PGs are distributed across OSDs, often using `pg-upmap` or related modes. The autoscaler addresses PG granularity; the balancer addresses distribution quality.

### Deep technical explanation

- Autoscaler decisions depend on current and expected pool usage. `target_size_ratio`, `target_size_bytes`, and bias can improve forecasts for pools that will grow.
- Balancer operation should be monitored for planned movement and compatibility with the minimum client version.
- Neither feature fixes an incorrect CRUSH topology, mixed drive performance, failing hardware, or insufficient capacity.
- During major upgrades or unstable incidents, avoid introducing unnecessary PG splits, merges, or large rebalancing operations.

### Useful commands

```bash
ceph osd pool autoscale-status
ceph osd pool set <pool> pg_autoscale_mode on
ceph balancer status
ceph balancer eval
ceph balancer optimize <plan>
```


### L3 validation points

- Review movement estimates.
- Confirm cluster health before large changes.
- Validate pool target ratios.

### Common mistake

Expecting the balancer to correct hot objects or application skew within a PG.

### Strong L3 closing statement

> I use autoscaling for PG sizing and balancing for placement quality, while separately addressing workload hotspots and hardware asymmetry.


## Q8. How do full, backfillfull, and nearfull ratios affect cluster behavior?

### Interview answer

Nearfull generates early warning. Backfillfull prevents new backfill to an OSD that has crossed the configured threshold. Full can block writes to protect data integrity when placement cannot safely accept more data. These ratios are safety controls, not routine capacity-management tools.

### Deep technical explanation

- Operational headroom is needed for OSD or host failure, recovery, BlueStore fragmentation, pool imbalance, and uneven device utilization.
- Cluster-wide free capacity can look acceptable while one OSD or device class approaches full because data is not perfectly uniform.
- Raising full ratios during an incident delays the failure but may create a more dangerous and harder-to-recover state.
- Capacity forecasting must trigger expansion well before nearfull, allowing for procurement, deployment, and rebalancing time.

### Useful commands

```bash
ceph osd dump | grep -E 'full_ratio|backfillfull_ratio|nearfull_ratio'
ceph osd df tree
ceph df detail
ceph health detail
```


### L3 validation points

- Find the fullest OSD and the reason for skew.
- Estimate failure-state utilization.
- Check pool and device-class distribution.

### Common mistake

Looking only at `ceph df` total free space and ignoring the most utilized OSD.

### Strong L3 closing statement

> Full ratios are last-resort guardrails. My capacity process preserves recovery headroom so the cluster never depends on moving those limits.



---

# OSD, BlueStore, and Data Integrity

## Q1. Explain BlueStore architecture and the purpose of block, block.db, and block.wal.

### Interview answer

BlueStore writes object data directly to raw block devices instead of using a conventional filesystem. The primary `block` device stores object data. `block.db` stores RocksDB metadata and small data according to allocation behavior. `block.wal` can hold the write-ahead log when placed on a separate faster device, although in many designs WAL is colocated with DB. Fast DB/WAL media can improve HDD-backed OSD metadata and small-write performance when sized and shared correctly.

### Deep technical explanation

- BlueStore uses checksums and an allocator, and BlueFS provides a filesystem-like environment for RocksDB files.
- A dedicated DB device helps only when it is materially faster and not oversubscribed. Shared NVMe failure can affect many HDD OSDs, so failure-domain and endurance analysis are required.
- Insufficient DB space can spill RocksDB data back to the slow device, causing latency. Excessively small partitions create operational debt.
- BlueStore does not need XFS on the OSD data device. The OSD data directory on the host contains links and metadata for the raw devices.

### Useful commands

```bash
ceph-volume lvm list
ceph device ls
ceph daemon osd.<id> perf dump
ceph-bluestore-tool show-label --dev <device>
ls -l /var/lib/ceph/<fsid>/osd.<id>/
```


### L3 validation points

- Check DB/WAL allocation and spillover signals.
- Review NVMe endurance and shared failure impact.
- Correlate RocksDB compaction with latency.

### Common mistake

Placing DB/WAL on a slower or equally slow device and expecting a performance improvement.

### Strong L3 closing statement

> BlueStore performance depends on the complete media hierarchy, DB sizing, endurance, and contention—not merely on assigning an NVMe path.


## Q2. What is the OSD lifecycle from deployment to safe removal?

### Interview answer

The lifecycle includes device validation, OSD creation, authentication and CRUSH placement, normal operation, maintenance, failure handling, data evacuation, daemon removal, and device zap or reuse. Safe removal requires confirming that data can be remapped, the cluster has sufficient capacity, and the OSD is safe to destroy before wiping the device.

### Deep technical explanation

- With cephadm, OSD service specifications declaratively select devices by path, model, size, rotational property, or other filters.
- Before removal, check `ok-to-stop` and `safe-to-destroy`. Marking an OSD out triggers data movement; doing many at once can overload recovery or exceed capacity.
- After replacement, verify CRUSH location, device class, weight, encryption policy, and OSD performance.
- Preserve failure evidence before zapping a failed disk if hardware or data-integrity analysis is required.

### Useful commands

```bash
ceph orch device ls --wide
ceph orch daemon add osd <host>:<device>
ceph orch daemon stop osd.<id>
ceph osd safe-to-destroy <id>
ceph orch osd rm <id> --replace
```


### L3 validation points

- Confirm recovery headroom.
- Remove one failure domain at a time unless the design explicitly supports more.
- Validate post-replacement PG state.

### Common mistake

Zapping a device before confirming that the OSD is safe to destroy and that required evidence has been collected.

### Strong L3 closing statement

> OSD replacement is a data-movement change, not just a hardware swap; I gate it on capacity, safety checks, and recovery impact.


## Q3. What do OSD `up/down` and `in/out` states mean?

### Interview answer

`up/down` describes whether the OSD daemon is running and reachable. `in/out` describes whether CRUSH should place data on that OSD. An OSD can be down but still in, creating degraded PGs while Ceph waits for it to return, or up but out, where it runs but should not receive normal placement.

### Deep technical explanation

- `down+in` is common during a transient failure. After the configured interval or administrative action, the OSD may be marked out and data remapped.
- `up+out` can occur during maintenance, replacement, or manual reweighting.
- Repeated flapping causes peering and recovery churn. Investigate power, storage latency, kernel resets, network drops, container restarts, and host pressure.
- Flags such as `noout`, `norebalance`, or `nobackfill` can alter automatic behavior and must be tracked carefully.

### Useful commands

```bash
ceph osd tree
ceph osd stat
ceph osd dump | grep flags
ceph health detail
ceph orch ps --daemon_id <id>
```


### L3 validation points

- Establish whether failure is daemon, host, disk, or network.
- Check active flags before interpreting recovery behavior.
- Avoid leaving maintenance flags set.

### Common mistake

Marking an intermittently failing OSD back in repeatedly without resolving the underlying fault.

### Strong L3 closing statement

> I interpret OSD state together with PG state, host health, and cluster flags to avoid causing repeated remapping.


## Q4. How do scrub and deep-scrub protect data integrity?

### Interview answer

Scrub compares object metadata and replica information within a PG. Deep-scrub additionally reads object data and verifies checksums, allowing detection of silent corruption. Scrubbing is a scheduled integrity process; it is not a replacement for redundancy, media monitoring, backups, or application-level validation.

### Deep technical explanation

- Scrub load competes with foreground I/O, so scheduling and throttling must balance integrity objectives and workload latency.
- Inconsistent PGs require careful analysis to identify the authoritative copy. Automatic or manual repair should follow documented procedures.
- Frequent scrub errors can indicate failing media, controller or cable faults, memory problems, kernel issues, or software defects.
- Do not suppress scrub warnings permanently. Track overdue scrubs and understand why the schedule cannot complete.

### Useful commands

```bash
ceph health detail
ceph pg dump | grep -i scrub
ceph pg deep-scrub <pgid>
rados list-inconsistent-pg <pool>
rados list-inconsistent-obj <pgid> --format json-pretty
```


### L3 validation points

- Determine whether inconsistency is metadata or data.
- Collect SMART and kernel evidence.
- Verify repair and subsequent deep-scrub.

### Common mistake

Running `ceph pg repair` blindly without identifying the correct object copy and hardware root cause.

### Strong L3 closing statement

> Scrubbing is a detection mechanism; the L3 task is to preserve evidence, select the authoritative data, repair safely, and eliminate the source of corruption.


## Q5. How do you troubleshoot high OSD commit and apply latency?

### Interview answer

First confirm whether latency is isolated to specific OSDs, hosts, device classes, or the entire cluster. Correlate OSD latency with device service time, queue depth, BlueStore DB/WAL behavior, RocksDB compaction, CPU steal or throttling, memory pressure, network delay, recovery traffic, and application workload. Fix the limiting layer instead of tuning OSD parameters immediately.

### Deep technical explanation

- High commit latency can indicate slow persistence to the underlying media or WAL path. Apply latency includes additional work before the operation is fully applied.
- Compare `ceph osd perf` with `iostat -x`, device SMART/NVMe logs, kernel errors, and host CPU/PSI.
- A single slow OSD can delay PG writes because the primary waits for required replicas or shards.
- Recovery, backfill, scrub, snapshots, RGW bucket-index activity, or RocksDB compaction may create temporary spikes.
- Benchmarking a production OSD destructively is unacceptable. Use supported, non-destructive diagnostics and isolated test devices.

### Useful commands

```bash
ceph osd perf
iostat -xz 1
pidstat -dru -p $(pgrep -d, ceph-osd) 1
cat /proc/pressure/io
journalctl -k --since '-30 min'
```


### L3 validation points

- Compare affected and healthy OSDs.
- Check the full replica path, not only the primary.
- Correlate to recovery and compaction windows.

### Common mistake

Increasing operation queues or recovery threads before proving that queueing, rather than media saturation, is the bottleneck.

### Strong L3 closing statement

> OSD latency is an end-to-end symptom. I correlate Ceph, block, CPU, memory, and network evidence before changing any tuning.


## Q6. What causes BlueStore slow operations, and how do you investigate them?

### Interview answer

Slow operations occur when an OSD request exceeds the warning threshold. Causes include slow or failing media, blocked replication, network loss, overloaded CPUs, BlueStore/RocksDB stalls, memory pressure, full conditions, recovery contention, or a blocked PG dependency. Investigation starts with the exact slow-op description and the affected daemon and PG.

### Deep technical explanation

- Use health detail and OSD logs to determine the operation type, age, PG, and blocking stage.
- Check whether the OSD is waiting for replicas, waiting for map updates, blocked in BlueStore, or unable to dispatch due to resource saturation.
- One failed network path may affect only replica traffic, producing write latency while reads appear normal.
- Persistent slow ops should trigger hardware and kernel investigation; clearing warnings without root cause is not resolution.

### Useful commands

```bash
ceph health detail
ceph daemon osd.<id> dump_historic_ops
ceph daemon osd.<id> dump_ops_in_flight
ceph pg <pgid> query
journalctl -u 'ceph-*.service' --since '-30 min'
```


### L3 validation points

- Identify operation stage and dependency.
- Check OSD heartbeat and network counters.
- Capture evidence before restarting.

### Common mistake

Restarting the OSD immediately and losing the in-flight operation evidence.

### Strong L3 closing statement

> I use the slow-op trace to identify the blocked stage, preserve evidence, and restart only when the operational risk justifies it.


## Q7. How should HDD, SSD, and NVMe OSDs be mixed in one cluster?

### Interview answer

Different media can coexist, but they should normally be represented by correct device classes and targeted by separate CRUSH rules and pools. Workloads should be placed intentionally based on latency, endurance, throughput, and cost. Mixing media in the same acting set can make the slowest device dominate write latency.

### Deep technical explanation

- Metadata pools, RGW indexes, and latency-sensitive RBD workloads often benefit from faster media, subject to sizing and endurance.
- Capacity pools may use HDD data devices with SSD/NVMe DB/WAL, but shared fast devices must be sized for throughput and failure impact.
- Device class is not a substitute for performance validation; drives within one class can differ greatly.
- Reclassification or CRUSH rule changes move data and should be planned like a migration.

### Useful commands

```bash
ceph osd crush class ls
ceph osd crush class ls-osd <class>
ceph osd crush rule create-replicated <rule> <root> <failure-domain> <class>
ceph osd pool set <pool> crush_rule <rule>
```


### L3 validation points

- Benchmark by media model and firmware.
- Track endurance and thermal throttling.
- Validate failure-domain placement within each class.

### Common mistake

Creating a single mixed-media pool and expecting CRUSH to automatically optimize objects by latency.

### Strong L3 closing statement

> I expose media tiers as explicit service classes with separate pools, CRUSH rules, SLOs, and capacity plans.


## Q8. How do you diagnose an OSD that repeatedly crashes or flaps?

### Interview answer

Correlate cephadm/container logs, systemd events, kernel messages, disk health, network errors, memory pressure, and the crash archive. Determine whether the daemon process crashed, the container was restarted, the host rebooted, the device reset, or the OSD lost heartbeat connectivity. Preserve crash dumps and avoid repeated automatic reintegration until the root cause is understood.

### Deep technical explanation

- Check `ceph crash ls-new` and inspect the crash metadata and backtrace.
- Kernel logs may show NVMe resets, SCSI errors, machine checks, OOM kills, soft lockups, or NIC resets.
- Host pressure can cause heartbeat timeouts even when the disk is healthy.
- If the OSD re-enters and leaves repeatedly, set an appropriate maintenance state or stop it to prevent cluster churn while investigating.
- After remediation, verify stable uptime, clean PGs, SMART health, and no new crashes.

### Useful commands

```bash
ceph crash ls-new
ceph crash info <crash-id>
ceph orch logs --daemon_name osd.<id> -- --since 1h
journalctl -k -b
smartctl -x <device>
```


### L3 validation points

- Build a timestamped event timeline.
- Differentiate process crash from connectivity loss.
- Check host-wide symptoms affecting multiple OSDs.

### Common mistake

Replacing the disk solely because the OSD is down, without checking process, network, power, and host-level evidence.

### Strong L3 closing statement

> Flapping is a systems problem. I stabilize the cluster first, preserve evidence, isolate the failing layer, and only then replace or reintroduce the OSD.



---

# Cephadm Deployment and Operations

## Q1. What is cephadm, and how does it manage RHCS clusters?

### Interview answer

`cephadm` is the container-based deployment and lifecycle mechanism used by modern Ceph clusters. It bootstraps the first MON and MGR, manages hosts through the orchestrator interface, deploys daemon containers, applies declarative service specifications, manages images and upgrades, and integrates monitoring services. Day-two operations are normally performed with `ceph orch` from a `cephadm shell` or configured administrative host.

### Deep technical explanation

- Cephadm stores the desired service state in the cluster and reconciles actual daemons against service specifications.
- Containers isolate daemon packages from the host package set, but the host operating system, container runtime, time sync, networking, storage devices, and security configuration remain critical.
- Administrative keys and configuration should be distributed only where required. Host labels can control placement, such as `_admin` for configuration and keyring distribution.
- Manual container changes can be overwritten by reconciliation; use supported orchestrator commands and specifications.

### Useful commands

```bash
cephadm shell -- ceph -s
ceph orch host ls
ceph orch ls --export
ceph orch ps
cephadm version
```


### L3 validation points

- Confirm desired versus actual service state.
- Validate container image provenance.
- Use service specifications under change control.

### Common mistake

Managing cephadm daemon containers directly with ad-hoc Podman commands as the normal operational method.

### Strong L3 closing statement

> Cephadm is a reconciliation system; I make supported declarative changes through the orchestrator and verify convergence.


## Q2. Describe a safe production bootstrap process.

### Interview answer

A safe bootstrap begins with supported RHEL hosts, synchronized time, correct hostname and DNS, required repositories and container registry access, redundant networks, firewall rules, and clean storage devices. Bootstrap the first MON/MGR with explicit network and dashboard decisions, securely store the generated configuration and keys, then add hosts and deploy redundant control services before OSDs and client-facing services.

### Deep technical explanation

- Choose the initial monitor address carefully; changing network design later is disruptive.
- Use a registry authentication file where required and pin supported images rather than relying on uncontrolled tags.
- Immediately add additional MON and MGR instances across independent failure domains.
- Record FSID, monitor endpoints, container image digest, bootstrap options, and generated credentials.
- Validate quorum, orchestration, monitoring, and host checks before provisioning all disks.

### Useful commands

```bash
cephadm bootstrap --mon-ip <ip> --cluster-network <cidr>
ceph orch host add <host> <ip>
ceph orch apply mon --placement='3 host1 host2 host3'
ceph orch apply mgr --placement='2 host1 host2'
ceph cephadm check-host <host>
```


### L3 validation points

- Test host prerequisites before adding disks.
- Verify control-plane redundancy.
- Protect bootstrap credentials.

### Common mistake

Deploying all OSDs before validating host, network, and control-plane design.

### Strong L3 closing statement

> I bootstrap the smallest viable control plane, validate it, and then expand declaratively in controlled stages.


## Q3. What is a cephadm service specification, and why is it important?

### Interview answer

A service specification is YAML or JSON describing the desired deployment of a daemon service: service type, service ID, placement, networks, ports, container settings, and service-specific parameters. It provides repeatable, reviewable, declarative operations and enables cephadm to reconcile failed or missing daemons.

### Deep technical explanation

- Placement can use explicit hosts, labels, count, or host patterns. OSD specs can select devices by paths, model, size, rotational property, and other filters.
- Specifications should be stored in version control with environment-specific secrets excluded.
- Broad filters such as `all-available-devices` can consume newly attached disks automatically; this is useful only when the operational model expects it.
- Export the running specs before a change and compare the desired state after applying modifications.

### Useful commands

```bash
ceph orch ls --export > cluster-spec.yaml
ceph orch apply -i service.yaml
ceph orch ls --service_name <service> --format yaml
ceph orch apply osd -i osd-spec.yaml --dry-run
```


### L3 validation points

- Review placement and device filters.
- Use dry-run where supported.
- Verify no unintended daemon movement.

### Common mistake

Using a wildcard device specification without governance, causing an attached maintenance or replacement disk to be consumed unexpectedly.

### Strong L3 closing statement

> Service specifications turn cluster operations into audited infrastructure-as-code and reduce one-off configuration drift.


## Q4. How do you place a host into maintenance safely?

### Interview answer

First evaluate whether the daemons on the host can stop without losing quorum, filesystem availability, gateway capacity, or required data redundancy. Use orchestrator maintenance checks and enter maintenance mode through `ceph orch`, monitor PG and service health, complete the host work, then exit maintenance and verify daemon reconciliation and clean recovery.

### Deep technical explanation

- `ok-to-stop` checks daemon safety, but the operator must also evaluate client-facing capacity such as RGW, NFS, iSCSI, monitoring, and load balancers.
- `noout` behavior may be applied by maintenance workflows to avoid unnecessary data movement during a short outage. Long maintenance can leave the cluster degraded and increase risk.
- Only one failure domain should normally be serviced at a time unless the design and test evidence support more.
- Define abort criteria such as loss of MON quorum, inactive PGs, excessive latency, or another host failure.

### Useful commands

```bash
ceph orch host ok-to-stop <host>
ceph orch host maintenance enter <host>
ceph -s
ceph orch host maintenance exit <host>
ceph orch ps --host <host>
```


### L3 validation points

- Check application redundancy.
- Set a maximum maintenance duration.
- Verify flags and PG recovery after exit.

### Common mistake

Assuming maintenance mode guarantees application availability for every gateway and external dependency.

### Strong L3 closing statement

> Maintenance safety includes the entire service path, not just OSD redundancy.


## Q5. How do you add capacity without creating a performance incident?

### Interview answer

Validate hardware and firmware, network and power capacity, device identity, and OSD specifications. Add capacity in controlled batches, monitor data movement and foreground latency, and adjust recovery concurrency only from evidence. Verify CRUSH location, weights, device classes, and final distribution before closing the change.

### Deep technical explanation

- Adding a large empty host can attract substantial data. The balancer and CRUSH distribute data, but rebalancing consumes network, disk, and CPU.
- Batch size should be small enough that another failure can be tolerated during the change.
- Do not add a different drive model to an existing service class without testing latency and endurance.
- Capacity expansion should happen before nearfull, leaving time for safe rebalancing.

### Useful commands

```bash
ceph orch device ls --wide
ceph orch apply osd -i osd-spec.yaml --dry-run
ceph -w
ceph progress
ceph osd df tree
```


### L3 validation points

- Monitor client latency percentiles.
- Confirm recovery does not saturate links.
- Validate post-change failure-state headroom.

### Common mistake

Deploying all new OSDs at once and then aggressively increasing recovery settings to finish quickly.

### Strong L3 closing statement

> Expansion is a controlled rebalancing event; I protect client SLOs and preserve one-failure-domain safety throughout.


## Q6. How do you manage cluster flags such as noout, norebalance, nobackfill, norecover, and pause?

### Interview answer

Cluster flags temporarily alter automatic placement or I/O behavior. `noout` prevents automatic out marking, `norebalance` and `nobackfill` restrict data movement, `norecover` blocks recovery, and `pause` can stop client I/O. They must be used only for defined maintenance or incident objectives, with owner, expiry time, monitoring, and explicit removal.

### Deep technical explanation

- Flags can hide a worsening condition. For example, leaving `noout` set after a failure prevents restoration of redundancy.
- Multiple flags interact; always inspect the complete active flag set.
- Use the narrowest scope and shortest duration supported by the operational procedure.
- Alert on long-lived flags and include them in every handover.

### Useful commands

```bash
ceph osd dump | grep flags
ceph osd set noout
ceph osd unset noout
ceph osd set-group noout <bucket>
ceph osd unset-group noout <bucket>
```


### L3 validation points

- Document why, who, and when.
- Set a removal checkpoint.
- Verify recovery resumes after unsetting.

### Common mistake

Setting several flags during troubleshooting and forgetting which one is blocking recovery.

### Strong L3 closing statement

> Flags are emergency controls, not configuration defaults; every flag change has an expiry and a verification step.


## Q7. How do you collect a support-quality diagnostic bundle?

### Interview answer

Capture cluster status, health detail, versions, maps, service inventory, configuration, crash records, recent daemon logs, host resource data, kernel logs, storage health, network counters, and a precise incident timeline. Use Red Hat-supported collection tools where available, sanitize secrets, and preserve evidence before restarts or destructive actions.

### Deep technical explanation

- `ceph report` and `ceph status` provide cluster-level state, but host and application evidence is also required.
- Collect from affected and healthy comparison hosts.
- Record exact timezone and clock synchronization so events can be correlated.
- Large log collections should be bounded by the incident window and stored securely.
- Include recent changes, workload pattern, impact, start time, attempted mitigations, and rollback status.

### Useful commands

```bash
ceph report > ceph-report-$(date +%F-%H%M).json
ceph config dump > ceph-config.txt
ceph orch ps --format json-pretty > orch-ps.json
sos report
journalctl --since '<start-time>' > journal.txt
```


### L3 validation points

- Protect keys and customer data.
- Preserve unmodified originals.
- Include application-side latency and errors.

### Common mistake

Restarting all affected daemons before collecting logs, crash data, and in-flight operation state.

### Strong L3 closing statement

> A useful support bundle is a synchronized evidence set that allows another engineer to reconstruct the incident without guessing.


## Q8. What is configuration precedence in a modern Ceph cluster?

### Interview answer

Ceph configuration can come from compiled defaults, monitor configuration database values, local configuration files, command-line arguments, and daemon-specific overrides. In cephadm clusters, the monitor configuration database is the primary managed configuration source. Effective values must be checked at the relevant daemon scope.

### Deep technical explanation

- Configuration can be global, service-type, host, daemon, or other scoped sections depending on the option and release.
- A value shown in `ceph config dump` may not prove the effective runtime value if a higher-precedence source exists.
- Use `ceph config assimilate-conf` only as part of a planned migration, and avoid maintaining parallel unmanaged files.
- Record every non-default tuning with reason, benchmark evidence, owner, and rollback.

### Useful commands

```bash
ceph config dump
ceph config get osd.<id> <option>
ceph config set osd <option> <value>
ceph config rm osd <option>
ceph daemon osd.<id> config show
```


### L3 validation points

- Query effective values on affected daemons.
- Check whether the option is runtime-changeable.
- Remove obsolete overrides.

### Common mistake

Editing `/etc/ceph/ceph.conf` on one host and assuming all cephadm containers use that value.

### Strong L3 closing statement

> I verify configuration at the effective daemon scope and keep tuning in the managed configuration database under change control.



---

# RBD Block Storage

## Q1. How does RBD provide block storage, and what are its main use cases?

### Interview answer

RBD presents a logical block image whose data is striped across RADOS objects in a pool. Clients use the kernel RBD driver or `librbd` through QEMU, libvirt, OpenStack, Kubernetes integrations, or applications. RBD is commonly used for VM disks, cloud volumes, databases, and persistent application storage because it supports thin provisioning, snapshots, clones, caching, and asynchronous mirroring.

### Deep technical explanation

- The image size is logical; physical allocation grows as objects are written.
- RBD clients calculate object placement and communicate directly with OSDs.
- Image features affect functionality and compatibility. Common features include layering, exclusive-lock, object-map, fast-diff, deep-flatten, and journaling.
- Pool design, object size, image feature set, cache policy, and client queueing influence performance.

### Useful commands

```bash
rbd pool init <pool>
rbd create <pool>/<image> --size 100G
rbd info <pool>/<image>
rbd du <pool>/<image>
rbd status <pool>/<image>
```


### L3 validation points

- Confirm client compatibility with enabled features.
- Set pool application metadata.
- Measure actual allocated capacity.

### Common mistake

Assuming an RBD image consumes its full logical size immediately.

### Strong L3 closing statement

> RBD is a distributed block layer; I manage both the image lifecycle and the underlying pool, feature, and client semantics.


## Q2. Explain RBD snapshots, clones, flattening, and deep-flatten.

### Interview answer

An RBD snapshot captures image state at a point in time. A protected snapshot can serve as the parent of copy-on-write clones. Clones initially reference unchanged parent data, making them fast and space-efficient. Flatten copies referenced parent data into the child, removing the dependency. Deep-flatten can also remove dependencies through snapshot chains when supported.

### Deep technical explanation

- Snapshots are crash-consistent unless the guest or application is quiesced and flushed.
- Long clone chains increase operational complexity and can affect reads, deletion, and capacity planning.
- Deleting a parent image or snapshot requires resolving dependent clones.
- Fast-diff and object-map can accelerate used-space and difference calculations but require compatible image features and clean metadata.

### Useful commands

```bash
rbd snap create <pool>/<image>@snap1
rbd snap protect <pool>/<image>@snap1
rbd clone <pool>/<image>@snap1 <pool>/<clone>
rbd flatten <pool>/<clone>
rbd children <pool>/<image>@snap1
```


### L3 validation points

- Coordinate application quiescing.
- Track clone dependency chains.
- Verify capacity impact of flattening.

### Common mistake

Calling a storage snapshot an application-consistent backup without coordinating filesystem and database flushes.

### Strong L3 closing statement

> I distinguish crash consistency, application consistency, and independent backup retention when designing RBD snapshot workflows.


## Q3. What RBD image features are important for production?

### Interview answer

`layering` enables clones. `exclusive-lock` coordinates single-writer ownership and is a dependency for some advanced features. `object-map` tracks allocated objects. `fast-diff` accelerates incremental difference operations. `deep-flatten` helps remove parent relationships. `journaling` records writes for journal-based mirroring but adds write overhead. Features must match the client and disaster-recovery design.

### Deep technical explanation

- Enabling unsupported features can make an image inaccessible to older kernels, hypervisors, or integrations.
- Object-map can become invalid after unclean events and may require rebuild.
- Journaling can substantially increase write latency because writes are recorded in the journal before normal image modification.
- Feature policies should be standardized per workload class.

### Useful commands

```bash
rbd feature enable <pool>/<image> exclusive-lock,object-map,fast-diff
rbd feature disable <pool>/<image> journaling
rbd object-map check <pool>/<image>
rbd object-map rebuild <pool>/<image>
```


### L3 validation points

- Verify all clients before enabling features.
- Benchmark journaling overhead.
- Monitor object-map health.

### Common mistake

Enabling every available feature by default.

### Strong L3 closing statement

> RBD features are compatibility and behavior choices, not free enhancements; I enable only those required by the service design.


## Q4. How does RBD mirroring work, and when would you choose journal versus snapshot mode?

### Interview answer

RBD mirroring asynchronously replicates images between clusters using `rbd-mirror`. Journal mode replays a write journal and provides low RPO but adds write amplification and latency. Snapshot mode periodically creates mirror snapshots and copies changed extents, usually reducing steady write overhead at the cost of an RPO tied to the snapshot schedule. Neither mode provides automatic application-level consistency.

### Deep technical explanation

- Mirroring can be enabled per pool or image, depending on design.
- Promotion and demotion must prevent split brain. Disaster procedures must define which site is authoritative.
- Snapshot mode benefits from fast-diff and an appropriate schedule.
- Network bandwidth must handle changed data plus catch-up after outages.
- Test failover, failback, resync, and application startup—not only replication status.

### Useful commands

```bash
rbd mirror pool enable <pool> image
rbd mirror pool peer bootstrap create <pool>
rbd mirror pool status <pool> --verbose
rbd mirror image enable <pool>/<image> snapshot
rbd mirror image status <pool>/<image>
```


### L3 validation points

- Measure achievable RPO under peak writes.
- Control promotion authority.
- Test failback and divergence handling.

### Common mistake

Treating asynchronous mirroring as zero-data-loss synchronous replication.

### Strong L3 closing statement

> I select journal or snapshot mirroring from RPO, write-latency budget, bandwidth, and operational failover requirements.


## Q5. How do you troubleshoot slow RBD workloads?

### Interview answer

Separate guest/application latency from host, client, network, pool, PG, and OSD latency. Check I/O size, read/write mix, queue depth, flush frequency, cache mode, image features, pool type, replica or EC path, slow OSDs, recovery load, and the hypervisor or container storage stack. Compare multiple images and hosts to identify scope.

### Deep technical explanation

- Small synchronous writes are sensitive to replica latency and flush semantics.
- One slow OSD in an acting set can affect only a subset of images or extents.
- Deep clone chains, journaling, disabled caching, or object-map issues can add overhead.
- Hypervisor CPU contention, virtqueue settings, and guest filesystem behavior may dominate even when Ceph is healthy.
- Use fio only with a safe test image and a workload model that represents production.

### Useful commands

```bash
rbd status <pool>/<image>
rbd perf image iostat <pool>/<image>
ceph osd perf
ceph pg map <pgid>
fio --filename=/dev/rbdX --name=test --rw=randread --bs=4k --iodepth=32 --runtime=60 --time_based
```


### L3 validation points

- Do not run destructive tests on production images.
- Correlate latency across guest, client, and OSD.
- Compare healthy and affected paths.

### Common mistake

Benchmarking `/dev/rbdX` with a destructive write test against a live filesystem.

### Strong L3 closing statement

> RBD latency must be traced end to end; I prove where queueing begins before changing Ceph or guest parameters.


## Q6. How do you remove an RBD image safely?

### Interview answer

Confirm that no client is using the image, identify snapshots and clone dependencies, capture required backup or audit evidence, and move the image to the trash rather than deleting it immediately when policy allows. Purge only after the retention window and dependency checks. In OpenStack or Kubernetes environments, remove through the owning control plane to avoid orphaned metadata.

### Deep technical explanation

- `rbd status` shows watchers but absence of watchers does not prove the application no longer references the image.
- Snapshots with dependent clones block deletion.
- Trash provides a recovery window but still consumes storage.
- Orphan cleanup must reconcile the storage object with the cloud or CSI database.

### Useful commands

```bash
rbd status <pool>/<image>
rbd snap ls <pool>/<image>
rbd children <pool>/<image>@<snap>
rbd trash mv <pool>/<image>
rbd trash ls <pool>
```


### L3 validation points

- Validate ownership in the consuming platform.
- Check clone dependencies.
- Use retention and approval.

### Common mistake

Deleting an image directly from Ceph while OpenStack, Kubernetes, or another orchestrator still believes the volume exists.

### Strong L3 closing statement

> The control plane that owns the volume must lead deletion; Ceph cleanup is the final reconciled step.


## Q7. What is the difference between RBD map, NBD map, and librbd access?

### Interview answer

Kernel RBD maps an image as a Linux block device using the in-kernel client. `rbd-nbd` exposes an image through the Linux NBD layer and can support feature combinations or workflows not available in a particular kernel client. `librbd` is a userspace library used by QEMU and other applications without requiring a mapped block device. The choice affects features, performance, upgrade dependencies, and operational visibility.

### Deep technical explanation

- Kernel clients depend on the host kernel’s Ceph feature support.
- QEMU with librbd can access images directly and avoids an extra local block mapping layer.
- NBD processes must be monitored and cleaned up after failures.
- Credential scope and keyring handling differ among integration methods.

### Useful commands

```bash
rbd map <pool>/<image> --id <user>
rbd device list
rbd unmap /dev/rbdX
rbd-nbd map <pool>/<image>
qemu-img info rbd:<pool>/<image>
```


### L3 validation points

- Check feature compatibility.
- Use least-privilege credentials.
- Document recovery for stale mappings.

### Common mistake

Assuming all access methods support identical features and failure behavior.

### Strong L3 closing statement

> I select the client path from supported features, operational ownership, and performance evidence—not convenience alone.


## Q8. How do you design RBD pools for OpenStack or virtualization?

### Interview answer

Separate workloads by SLO, failure policy, device class, and operational lifecycle. Common designs use distinct pools for images, volumes, backups, and ephemeral workloads, with appropriate application tags, CRUSH rules, PG policies, and cephx users. The design must account for clone workflows, snapshot volume, recovery bandwidth, and the effect of one workload on another.

### Deep technical explanation

- Golden images and clone-heavy workflows benefit from feature and dependency governance.
- Database volumes may need fast replicated media, while backup volumes may use capacity-oriented pools.
- Per-service cephx users should have only the required pool capabilities.
- OpenStack-specific integration must follow the supported compatibility guide for the RHCS and OpenStack releases.
- Do not create excessive pools without considering PG and operational overhead.

### Useful commands

```bash
ceph osd pool application enable <pool> rbd
rbd pool init <pool>
ceph osd pool set <pool> pg_autoscale_mode on
ceph auth get-or-create client.<service> mon 'profile rbd' osd 'profile rbd pool=<pool>'
```


### L3 validation points

- Map every pool to an SLO and owner.
- Test noisy-neighbor behavior.
- Validate client feature compatibility.

### Common mistake

Using one large pool for every tenant and workload, eliminating policy isolation and making troubleshooting harder.

### Strong L3 closing statement

> Pool separation is an operational and SLO boundary; I use it deliberately while controlling PG and management overhead.



---

# CephFS and NFS

## Q1. How does CephFS work, and what is the role of the MDS?

### Interview answer

CephFS stores file data in one or more data pools and filesystem metadata in a dedicated metadata pool. MDS daemons manage the namespace, inode metadata, directory operations, capabilities, and metadata journal. Clients communicate with MDS for metadata and generally access file data directly through OSDs.

### Deep technical explanation

- The metadata pool should use resilient, low-latency replicated storage because metadata loss can affect the entire filesystem.
- One active MDS is sufficient for basic operation; additional active ranks can scale metadata workloads.
- Standby MDS daemons provide failover. Standby-replay can follow an active rank’s journal to reduce failover time but has placement implications.
- Client capabilities allow caching while preserving coherency.

### Useful commands

```bash
ceph fs ls
ceph fs status
ceph mds stat
ceph fs dump
ceph osd pool application get <metadata-pool>
```


### L3 validation points

- Protect the metadata pool more conservatively than bulk data.
- Maintain sufficient standby MDS capacity.
- Measure metadata and data paths separately.

### Common mistake

Assuming MDS handles all file data I/O and therefore scaling MDS fixes every CephFS throughput issue.

### Strong L3 closing statement

> MDS scales metadata operations; OSD and network design still determine file data performance.


## Q2. How do you create and deploy a production CephFS service?

### Interview answer

Create separate metadata and data pools, enable the CephFS application, create the filesystem, deploy MDS daemons through cephadm placement, configure standby expectations and optional multiple active ranks, then mount with least-privilege client credentials. Validate failover, quotas, snapshots, and recovery behavior before production.

### Deep technical explanation

- Metadata pools should normally be replicated and on appropriate media.
- Data pools can be added for different layouts or media classes; directory layouts determine where file data is stored.
- Multiple active MDS ranks help highly parallel metadata workloads but increase design and upgrade complexity.
- Use service specifications and explicit placement across failure domains.

### Useful commands

```bash
ceph osd pool create cephfs_metadata
ceph osd pool create cephfs_data
ceph fs new cephfs cephfs_metadata cephfs_data
ceph orch apply mds cephfs --placement='2 host1 host2'
ceph fs set cephfs standby_count_wanted 1
```


### L3 validation points

- Test MDS failover.
- Set quotas and client caps.
- Monitor metadata pool latency and fullness.

### Common mistake

Using an erasure-coded metadata pool.

### Strong L3 closing statement

> A production CephFS design protects metadata first and validates failover and client behavior under realistic namespace load.


## Q3. What are active MDS ranks, standby, and standby-replay?

### Interview answer

An active MDS rank owns part of the CephFS metadata workload. A standby MDS waits to take over any eligible failed rank. A standby-replay daemon continuously follows a specific active rank’s journal, reducing failover time, but it is generally tied to that rank rather than acting as a universal standby.

### Deep technical explanation

- Increasing `max_mds` creates additional active ranks for metadata scaling.
- Dynamic subtree partitioning distributes directory subtrees across active ranks based on workload.
- Every active rank should have appropriate standby coverage, especially when standby-replay is enabled.
- MDS failover may pause metadata operations while journal replay and state recovery complete.

### Useful commands

```bash
ceph fs status
ceph fs get <fs>
ceph fs set <fs> max_mds <n>
ceph fs set <fs> allow_standby_replay true
ceph fs set <fs> standby_count_wanted <n>
```


### L3 validation points

- Measure failover time.
- Ensure standby placement is in independent failure domains.
- Watch metadata cache and recall pressure.

### Common mistake

Counting a standby-replay daemon as a generic standby for every rank.

### Strong L3 closing statement

> MDS HA is rank-aware; I size active and standby roles from metadata load and tested failover RTO.


## Q4. How do CephFS client capabilities and cephx permissions work?

### Interview answer

CephFS clients authenticate with cephx and receive MON, MDS, and OSD capabilities. MDS capabilities can restrict filesystem, path, read/write access, root-squash behavior, and other operations. OSD capabilities permit access only to the filesystem’s data and metadata pools. Least-privilege keys should be created per application or tenant.

### Deep technical explanation

- Path-restricted clients should not receive broad pool access that bypasses intended controls.
- Kernel and FUSE mounts need access to monitor endpoints and appropriate key material.
- Credential rotation must be coordinated with mounted clients.
- CephFS quotas are enforced through filesystem metadata, not by cephx alone.

### Useful commands

```bash
ceph fs authorize <fs> client.app /project rw
ceph auth get client.app
ceph auth caps client.app mon 'allow r' mds 'allow rw path=/project' osd 'allow rw tag cephfs data=<fs>'
ceph auth del client.app
```


### L3 validation points

- Test denied paths.
- Protect keyrings on clients.
- Use separate identities per service.

### Common mistake

Giving `client.admin` to every CephFS client.

### Strong L3 closing statement

> CephFS security is layered: cephx identity, MDS path permissions, OSD pool tags, and host-level mount controls.


## Q5. How do CephFS snapshots and quotas work?

### Interview answer

CephFS snapshots capture directory-tree state through special snapshot namespaces and metadata operations. Quotas can limit bytes or files on directories and are enforced by CephFS metadata accounting. Both features depend on healthy MDS operation and should be tested for application behavior, retention, and recovery impact.

### Deep technical explanation

- Snapshots are not external backups and can share the same cluster failure domain.
- Snapshot creation may be fast, but retained snapshots can prevent data reclamation and increase metadata load.
- Quota reporting and enforcement can have propagation behavior; applications should not rely on exact instantaneous accounting.
- Snapshot schedules and mirroring can support DR workflows when combined with remote-cluster procedures.

### Useful commands

```bash
mkdir /mnt/cephfs/project/.snap/snap1
setfattr -n ceph.quota.max_bytes -v 1099511627776 /mnt/cephfs/project
getfattr -n ceph.quota.max_bytes /mnt/cephfs/project
ceph fs snapshot mirror status <fs>
```


### L3 validation points

- Define retention and deletion ownership.
- Monitor metadata usage.
- Test restore procedures.

### Common mistake

Calling CephFS snapshots a ransomware-proof backup while they remain accessible in the same administrative domain.

### Strong L3 closing statement

> Snapshots improve recovery options, but independent protection and tested restore procedures are still required.


## Q6. How do you troubleshoot an MDS that is laggy or damaged?

### Interview answer

Check MDS state, beacon delays, host CPU and memory, metadata pool latency, network reachability, client sessions, slow requests, cache pressure, and journal replay. Determine whether the issue is an overloaded active rank, a slow metadata pool, a blocked client capability recall, or daemon failure. Preserve logs before failover or repair.

### Deep technical explanation

- A laggy MDS may miss monitor beacons because of CPU starvation, network loss, or internal stalls.
- Large client caches and unresponsive clients can delay capability recalls and metadata operations.
- Metadata pool full or slow conditions can affect the entire filesystem.
- Damaged metadata requires carefully documented recovery tools; avoid ad-hoc manipulation.

### Useful commands

```bash
ceph fs status
ceph health detail
ceph daemon mds.<name> dump_ops_in_flight
ceph daemon mds.<name> session ls
ceph daemon mds.<name> perf dump
```


### L3 validation points

- Check the metadata pool first.
- Identify problematic clients.
- Verify standby readiness before failover.

### Common mistake

Restarting every MDS simultaneously.

### Strong L3 closing statement

> I isolate whether the bottleneck is MDS compute, metadata storage, network, or client behavior before failing over or changing ranks.


## Q7. When would you use NFS-Ganesha in front of CephFS or RGW?

### Interview answer

NFS-Ganesha provides NFS access for clients that cannot use native CephFS or object APIs. It can export CephFS namespaces or other supported backends through a gateway tier. Use it when protocol compatibility is required, but account for gateway availability, session state, load balancing, lock behavior, and the extra performance layer.

### Deep technical explanation

- Native CephFS clients generally provide a more direct data path and richer Ceph integration.
- NFS gateway clusters need redundant placement and a supported virtual IP or ingress design.
- NFS semantics, identity mapping, Kerberos, firewall ports, and client mount options become part of the service.
- Gateway scale does not fix an underlying CephFS metadata or OSD bottleneck.

### Useful commands

```bash
ceph orch apply nfs <cluster-id> --placement='2 host1 host2'
ceph nfs cluster ls
ceph nfs export ls <cluster-id>
ceph orch ps --service_name nfs.<cluster-id>
```


### L3 validation points

- Test gateway failover and client recovery.
- Validate UID/GID mapping.
- Benchmark through the actual NFS path.

### Common mistake

Presenting NFS-Ganesha as a transparent protocol conversion with no availability or performance implications.

### Strong L3 closing statement

> I use NFS gateways for compatibility and design them as a separate HA service tier with their own SLOs.


## Q8. How do you tune CephFS for a metadata-intensive workload?

### Interview answer

First characterize operations such as creates, stats, renames, directory fanout, and client count. Place metadata on low-latency media, ensure adequate MDS CPU and memory, add active ranks only when the namespace can distribute effectively, manage client cache behavior, and reduce pathological directory layouts. Validate improvements with metadata latency and MDS performance counters.

### Deep technical explanation

- A single huge hot directory may not scale linearly across ranks.
- More MDS ranks increase coordination and should not be used as a default fix.
- Metadata pool OSD latency often dominates namespace performance.
- Client-side caching can help but may increase recall pressure during contention.
- Application redesign, such as sharding directories, can outperform storage tuning.

### Useful commands

```bash
ceph fs status
ceph daemon mds.<name> perf dump
ceph daemon mds.<name> dump cache status
ceph osd perf
mdtest -d /mnt/cephfs/test -n 100000 -i 3
```


### L3 validation points

- Use a safe test directory.
- Measure p95/p99 metadata latency.
- Check rank balance and hot directories.

### Common mistake

Increasing `max_mds` without confirming that metadata subtrees can be distributed.

### Strong L3 closing statement

> CephFS metadata scaling is a joint application-layout, MDS, metadata-pool, and client-cache problem.



---

# Ceph Object Gateway and S3

## Q1. What is the Ceph Object Gateway, and how does it scale?

### Interview answer

The Ceph Object Gateway, or RGW, provides S3- and Swift-compatible HTTP APIs over RADOS. RGW instances can be scaled horizontally behind load balancers. Object data, bucket indexes, users, metadata, and control information are stored in dedicated pools. Scaling must consider both stateless request processing and stateful backend structures such as bucket indexes.

### Deep technical explanation

- RGW daemon count increases request-processing capacity but does not automatically remove hot bucket-index or OSD bottlenecks.
- Placement targets and storage classes can map object categories to specific pools or device classes.
- Front-end, TLS, load balancer, DNS, and certificate design are part of the object service.
- Multi-tenancy uses RGW users, accounts, policies, quotas, and potentially external identity integration depending on supported release.

### Useful commands

```bash
ceph orch ls --service_type rgw
ceph orch ps --daemon-type rgw
radosgw-admin realm list
radosgw-admin zonegroup list
radosgw-admin zone list
```


### L3 validation points

- Measure per-bucket and per-gateway load.
- Validate load-balancer health checks.
- Separate data and index performance analysis.

### Common mistake

Calling RGW completely stateless and ignoring bucket index and metadata behavior.

### Strong L3 closing statement

> I scale the gateway tier and the RADOS backend independently, while tracking hot buckets and index pressure.


## Q2. Explain realm, zonegroup, zone, and period in RGW multisite.

### Interview answer

A realm is the top-level multisite namespace. A zonegroup contains one or more zones and typically represents a geographic or policy grouping. A zone is a specific RGW deployment backed by a Ceph cluster or pool set. A period captures the committed realm configuration and epoch. Multisite replicates metadata and object data asynchronously between zones.

### Deep technical explanation

- One zone is normally the metadata master for configuration and metadata changes according to the multisite design.
- Period updates must be committed and propagated correctly.
- Replication lag depends on change rate, network, shard distribution, gateway capacity, and backend performance.
- Failover and failback procedures must define promotion, DNS or load-balancer changes, and split-brain prevention.

### Useful commands

```bash
radosgw-admin realm get
radosgw-admin zonegroup get
radosgw-admin zone get
radosgw-admin period get
radosgw-admin period update --commit
```


### L3 validation points

- Track metadata and data sync status.
- Test promotion and resync.
- Document authoritative-site decisions.

### Common mistake

Assuming multisite is synchronous and guarantees zero RPO.

### Strong L3 closing statement

> RGW multisite is an asynchronous DR and geo-distribution system; I design it around measurable lag and controlled authority changes.


## Q3. How do you create and manage RGW users securely?

### Interview answer

Create separate users or accounts for applications, issue scoped access and secret keys, apply quotas and bucket policies, rotate credentials, and audit usage. Avoid sharing root-like credentials. Secrets must be delivered through a secure secrets system and never embedded in scripts or tickets.

### Deep technical explanation

- Users can have multiple keys to support rotation without downtime.
- Capabilities granted to administrative users should be restricted to the required RGW administration domains.
- Bucket policy and identity policy behavior should be validated against the supported S3 subset.
- Credential deletion must follow application cutover and audit verification.

### Useful commands

```bash
radosgw-admin user create --uid=app1 --display-name='Application 1'
radosgw-admin key create --uid=app1 --key-type=s3 --gen-access-key --gen-secret
radosgw-admin quota set --uid=app1 --quota-scope=user --max-size=1T --enabled=true
radosgw-admin user info --uid=app1
```


### L3 validation points

- Store generated secrets securely.
- Rotate with overlapping valid keys.
- Test least privilege.

### Common mistake

Printing secret keys into shared terminal logs or automation output.

### Strong L3 closing statement

> RGW identities are application credentials; I manage them with the same rotation, least-privilege, and audit controls as database passwords.


## Q4. What causes RGW bucket-index performance problems?

### Interview answer

Large or high-rate buckets can overload bucket-index shards, especially with many small objects, listings, deletes, or versioning. Diagnose shard distribution, index pool latency, hot gateways, and operation mix. Use supported dynamic resharding or planned shard sizing, and consider application bucket partitioning.

### Deep technical explanation

- Object data and bucket index paths have different bottlenecks.
- Too few shards create hotspots; too many shards increase overhead and management cost.
- Versioned buckets and lifecycle processing can increase index activity.
- Resharding is an operational change that must be monitored and coordinated.

### Useful commands

```bash
radosgw-admin bucket stats --bucket=<bucket>
radosgw-admin bucket limit check
radosgw-admin reshard status
radosgw-admin bucket reshard --bucket=<bucket> --num-shards=<n>
```


### L3 validation points

- Check index pool OSD latency.
- Measure list and delete workloads.
- Validate reshard completion.

### Common mistake

Adding RGW daemons when the actual bottleneck is a hot bucket index.

### Strong L3 closing statement

> I separate front-end request saturation from bucket-index and data-pool saturation before scaling.


## Q5. How do object versioning, lifecycle, and garbage collection affect capacity?

### Interview answer

Versioning retains previous object versions and delete markers, so logical user-visible size can differ greatly from physical consumption. Lifecycle policies can transition or expire objects and versions, but processing is asynchronous. Multipart uploads and deleted objects may leave temporary data for garbage collection. Capacity monitoring must include versions, incomplete uploads, GC queues, and replication lag.

### Deep technical explanation

- Lifecycle policies are not immediate deletion guarantees.
- Large backlogs can consume capacity and create sustained backend load.
- Object Lock or retention policies may intentionally prevent deletion.
- Capacity reports should distinguish current objects, noncurrent versions, incomplete multipart data, and overhead.

### Useful commands

```bash
radosgw-admin bucket stats --bucket=<bucket>
radosgw-admin gc list --include-all
radosgw-admin lc list
aws s3api list-multipart-uploads --bucket <bucket>
```


### L3 validation points

- Monitor lifecycle and GC backlog.
- Validate retention requirements.
- Forecast physical, not only logical, capacity.

### Common mistake

Assuming an S3 delete immediately releases all physical storage.

### Strong L3 closing statement

> Object capacity is lifecycle-driven; I monitor retained versions, queues, and incomplete operations, not just current object totals.


## Q6. How do you troubleshoot RGW 5xx errors or high latency?

### Interview answer

Trace the request from client DNS and TLS through the load balancer, RGW front end, authentication, bucket index, and data pools. Correlate request IDs with RGW logs. Check gateway CPU, connections, thread or async processing, backend OSD latency, full conditions, multisite sync, and external identity dependencies.

### Deep technical explanation

- HTTP 5xx can originate from the load balancer, gateway, or backend failure; identify the component that generated the response.
- High latency for LIST may indicate index issues, while GET or PUT latency may point to data pools or network.
- Authentication failures can appear during clock skew, key rotation, or external identity outages.
- Compare multiple gateways and buckets to determine scope.

### Useful commands

```bash
ceph orch logs --daemon_name rgw.<daemon> -- --since 30m
curl -vk https://<endpoint>/
radosgw-admin bucket stats --bucket=<bucket>
ceph osd perf
ss -s
```


### L3 validation points

- Capture request ID and operation type.
- Compare gateway and bucket scope.
- Check load balancer metrics.

### Common mistake

Restarting all RGW daemons without preserving request logs and determining whether the backend is slow.

### Strong L3 closing statement

> RGW troubleshooting is protocol-to-RADOS tracing; the HTTP code alone does not identify the failing layer.


## Q7. How do you design RGW high availability?

### Interview answer

Deploy multiple RGW instances across failure domains, place them behind redundant load balancers or an approved ingress service, provide resilient DNS and certificates, and ensure backend pools and MON connectivity remain available. Define health checks that validate real service readiness, not only an open TCP port.

### Deep technical explanation

- Gateway placement should avoid correlated host, rack, or site failure.
- Load balancers must preserve required headers and support the S3 request patterns and payload sizes.
- TLS termination location affects certificate ownership, encryption policy, and troubleshooting.
- Session affinity is generally not a substitute for correct gateway behavior.
- Test large uploads, multipart operations, and in-flight failure.

### Useful commands

```bash
ceph orch apply rgw <service-id> --placement='3 host1 host2 host3'
ceph orch ps --service_name rgw.<service-id>
curl -sS -o /dev/null -w '%{http_code} %{time_total}\n' https://<endpoint>/
```


### L3 validation points

- Test one-gateway and one-load-balancer failure.
- Validate certificate rotation.
- Check backend capacity during failover.

### Common mistake

Using a single virtual IP appliance without redundant control and data paths.

### Strong L3 closing statement

> RGW HA includes gateway, load balancer, DNS, TLS, and RADOS dependencies, all tested under in-flight workload.


## Q8. What should an RGW multisite disaster-recovery runbook contain?

### Interview answer

It should define health prerequisites, current sync status, acceptable data-loss window, authority for failover, write fencing, zone promotion, period changes, DNS/load-balancer cutover, application validation, monitoring, rollback, and failback or resync. Every step needs commands, expected output, abort criteria, and evidence capture.

### Deep technical explanation

- Confirm metadata and data sync lag before planned failover.
- During unplanned failover, record the last known synchronized markers and communicate possible RPO loss.
- Prevent simultaneous uncontrolled writes to divergent sites.
- Failback may require resynchronization and conflict handling; it is not simply reversing DNS.
- Run drills using representative buckets and object sizes.

### Useful commands

```bash
radosgw-admin sync status
radosgw-admin metadata sync status
radosgw-admin data sync status
radosgw-admin period get
radosgw-admin zone get
```


### L3 validation points

- Document RPO evidence.
- Fence or isolate the old primary.
- Validate read, write, list, delete, and multipart.

### Common mistake

Promoting the secondary without preventing the old site from accepting writes.

### Strong L3 closing statement

> A multisite DR procedure is an authority-management process as much as a replication process.



---

# Security, Hardening, and Governance

## Q1. What is cephx, and what security does it provide?

### Interview answer

Cephx authenticates Ceph clients and daemons and authorizes operations through capabilities. It prevents unauthenticated access and limits identities to specific monitor, manager, OSD pool, MDS path, or service operations. Cephx is not encryption; data-in-transit and data-at-rest controls must be designed separately.

### Deep technical explanation

- Each identity has a secret key and capability set.
- Daemon identities are created and managed by deployment tooling; application identities should be separated by service and environment.
- Capabilities can use profiles and pool, namespace, class, or CephFS tags.
- Keyrings are high-value secrets and require filesystem permissions, controlled distribution, rotation, and revocation.

### Useful commands

```bash
ceph auth ls
ceph auth get client.app
ceph auth get-or-create client.app mon 'allow r' osd 'allow rw pool=app'
ceph auth caps client.app mon 'allow r' osd 'allow r pool=app'
ceph auth del client.app
```


### L3 validation points

- Verify denied operations.
- Remove unused identities.
- Protect admin keys.

### Common mistake

Saying cephx encrypts user data.

### Strong L3 closing statement

> Cephx answers who may do what; encryption answers who can read captured or stolen data.


## Q2. How do you implement least privilege for Ceph administrators and applications?

### Interview answer

Create separate named identities for automation, monitoring, backup, RBD, CephFS, and RGW administration. Grant only required MON, MGR, OSD, MDS, or RGW capabilities. Restrict shell access and use centralized privilege escalation with audit logs. Avoid distributing `client.admin` beyond tightly controlled break-glass workflows.

### Deep technical explanation

- Read-only monitoring usually needs different capabilities from provisioning automation.
- Pool-specific RBD users should not access unrelated pools.
- CephFS path restrictions must be paired with correct OSD tags.
- Administrative actions should be attributable to an individual or controlled service identity.
- Review capabilities periodically and after platform migrations.

### Useful commands

```bash
ceph auth get-or-create client.monitor mon 'allow r' mgr 'allow r'
ceph auth get-or-create client.rbdapp mon 'profile rbd' osd 'profile rbd pool=app'
ceph auth ls --format json-pretty
```


### L3 validation points

- Map identities to owners.
- Test access boundaries.
- Rotate and revoke on ownership change.

### Common mistake

Using one shared admin key for humans, automation, OpenStack, and monitoring.

### Strong L3 closing statement

> Least privilege in Ceph requires separate identities, narrow capabilities, controlled host access, and periodic entitlement review.


## Q3. How is encryption in transit implemented in Ceph?

### Interview answer

Modern Ceph messenger v2 can provide authenticated and encrypted daemon and client communication when secure mode is configured and supported. RGW and dashboard normally use TLS for HTTP interfaces. Encryption policy must cover public and cluster networks, client compatibility, certificate lifecycle, and performance impact.

### Deep technical explanation

- Messenger v2 security is distinct from cephx authentication, though they work together.
- Legacy clients may force compatibility decisions; verify the minimum supported client release.
- TLS termination can occur at gateways or load balancers, but internal hops must match the security requirement.
- Encryption does not protect data on stolen disks; use at-rest encryption separately.

### Useful commands

```bash
ceph mon dump
ceph config dump | grep -E 'ms_cluster_mode|ms_service_mode|ms_client_mode'
ceph config get mon ms_cluster_mode
openssl s_client -connect <rgw-endpoint>:443 -servername <name>
```


### L3 validation points

- Confirm effective messenger modes.
- Inventory incompatible clients.
- Test certificate rotation.

### Common mistake

Assuming a private VLAN removes the need for encryption or authentication.

### Strong L3 closing statement

> I verify encryption on every required hop and treat client compatibility and key lifecycle as part of the design.


## Q4. How do you protect data at rest in a Ceph cluster?

### Interview answer

Use supported device encryption, commonly LUKS-managed OSD encryption through deployment tooling, combined with secure key handling, protected boot and host access, and encrypted client or gateway layers where required. At-rest encryption protects lost or stolen media but not an attacker with active administrative access to the running cluster.

### Deep technical explanation

- Encryption should be selected during OSD provisioning and documented in the OSD specification or workflow.
- Key availability affects automated boot and recovery; protect key-management dependencies.
- Securely erase or cryptographically retire replaced media according to policy.
- Backups, RGW logs, crash dumps, and support bundles may also contain sensitive data.

### Useful commands

```bash
ceph-volume lvm list
cryptsetup status <mapping>
lsblk -f
ceph orch device ls --wide
```


### L3 validation points

- Verify replacement and recovery procedures.
- Protect keys outside the failed disk.
- Test boot after key-management outage scenarios.

### Common mistake

Claiming disk encryption protects against a compromised root user on an online OSD host.

### Strong L3 closing statement

> At-rest encryption reduces offline media risk; host, identity, network, and backup controls still protect the running service.


## Q5. How do you secure cephadm and the container supply chain?

### Interview answer

Use supported signed images from approved registries, control registry credentials, pin approved image versions or digests where appropriate, restrict who can run cephadm or orchestrator commands, protect the cephadm SSH key, harden hosts, and audit image and configuration changes. Do not use arbitrary public images in production.

### Deep technical explanation

- Cephadm’s SSH key can control cluster hosts and is highly privileged.
- Registry credentials should be stored in protected files or secrets systems, not shell history.
- Image updates must follow RHCS lifecycle and upgrade guidance.
- Host container runtime, SELinux, firewall, patching, and filesystem permissions remain part of the attack surface.

### Useful commands

```bash
ceph config-key get mgr/cephadm/ssh_identity_key > /secure/path/key
ceph orch upgrade status
ceph orch ps --format json-pretty | jq -r '.[].container_image_name' | sort -u
podman image inspect <image>
```


### L3 validation points

- Inventory image digests.
- Limit SSH key exposure.
- Review break-glass access.

### Common mistake

Assuming containerization eliminates host-hardening requirements.

### Strong L3 closing statement

> Cephadm secures operations only when the image source, host, SSH trust, and administrative API are all governed.


## Q6. What should be audited in a Ceph environment?

### Interview answer

Audit administrative logins, ceph auth changes, pool and CRUSH changes, OSD and host lifecycle actions, cluster flags, configuration updates, upgrades, RGW administrative actions, dashboard access, and secret rotation. Correlate Ceph logs with operating-system audit logs, bastion records, ticket IDs, and automation pipelines.

### Deep technical explanation

- Ceph logs are operational evidence but may not provide complete human identity attribution if shared credentials are used.
- Command history is not a reliable audit control.
- High-risk changes should be executed through controlled automation or privileged-access tooling.
- Retention must support incident response and compliance while protecting customer data and secrets.

### Useful commands

```bash
ceph config log
ceph log last 100
ausearch -m USER_CMD,EXECVE --start today
journalctl --since today | grep -i ceph
```


### L3 validation points

- Use unique identities.
- Synchronize clocks.
- Protect logs from tampering.

### Common mistake

Relying on a shared root shell and bash history for accountability.

### Strong L3 closing statement

> Auditability starts with attributable identities and immutable change records, then uses Ceph and host telemetry for technical evidence.


## Q7. How do you secure the Ceph Dashboard?

### Interview answer

Use TLS with trusted certificates, strong named accounts or supported identity integration, role-based access, restricted network exposure, session controls, and regular account review. Disable or restrict unused modules, protect dashboard endpoints behind approved network controls, and monitor login and configuration activity.

### Deep technical explanation

- The dashboard is a management interface with high-impact capabilities and should not be exposed broadly.
- Default or bootstrap credentials must be changed and protected.
- Certificate expiry monitoring and rotation should be tested.
- Dashboard access does not replace host and CLI access governance.

### Useful commands

```bash
ceph mgr module ls
ceph mgr services
ceph dashboard ac-user-show
ceph dashboard ac-role-show
ceph dashboard set-ssl-certificate -i <cert-file>
```


### L3 validation points

- Test RBAC boundaries.
- Monitor certificate expiry.
- Restrict management network exposure.

### Common mistake

Publishing the dashboard directly to the internet because it uses HTTPS.

### Strong L3 closing statement

> I treat the dashboard as a privileged control-plane application and protect it accordingly.


## Q8. How do you prevent ransomware or accidental deletion from destroying Ceph data?

### Interview answer

Use layered controls: least privilege, MFA and privileged-access management, immutable or retention-controlled object policies where supported, snapshots with protected administration, independent backups in a separate security and failure domain, replication with controlled authority, deletion approvals, and tested restore procedures. Ceph replication alone is not backup because it replicates authorized deletion and corruption.

### Deep technical explanation

- Backup credentials should not be accessible from normal application or cluster-admin contexts.
- Object Lock, versioning, snapshot retention, and external backup each address different threats.
- Monitor bulk deletion, unusual API behavior, and rapid capacity changes.
- Recovery drills must include credential compromise and management-plane loss.

### Useful commands

```bash
rbd snap ls <pool>/<image>
ceph fs snapshot mirror status <fs>
radosgw-admin bucket stats --bucket=<bucket>
ceph auth ls
```


### L3 validation points

- Separate administrative domains.
- Test full restore.
- Define retention protected from routine admins.

### Common mistake

Calling three replicas or multisite replication a backup.

### Strong L3 closing statement

> Resilience keeps the service running; recoverability requires independent, access-separated copies and rehearsed restoration.



---

# Monitoring, Performance, and Capacity

## Q1. What are the first commands you run during Ceph triage?

### Interview answer

Start with cluster health and scope: `ceph -s`, `ceph health detail`, service inventory, OSD and PG status, capacity, recent crashes, and active flags. Then correlate with host, network, storage, and application metrics. The goal is to identify impact, affected failure domain, recent change, and whether the cluster is stable enough for deeper diagnosis.

### Deep technical explanation

- `HEALTH_WARN` is not a root cause; read each health check and determine its operational consequence.
- Compare current metrics to a known baseline.
- Establish whether the incident affects all clients, one pool, one host, one PG set, or one protocol.
- Preserve timestamps and avoid changing several variables at once.

### Useful commands

```bash
ceph -s
ceph health detail
ceph orch ps --refresh
ceph osd tree
ceph df detail
ceph crash ls-new
```


### L3 validation points

- State application impact.
- Find recent changes.
- Check active flags and fullness.

### Common mistake

Starting with configuration tuning before identifying scope and impact.

### Strong L3 closing statement

> My first five minutes produce a scope, risk, and evidence plan—not a speculative fix.


## Q2. Which Ceph performance metrics matter most?

### Interview answer

Key metrics include client IOPS, throughput, latency percentiles, OSD commit/apply latency, slow operations, PG states, recovery and backfill rate, OSD utilization and fullness, disk service time and queue depth, network drops and retransmissions, CPU and memory pressure, MDS metadata latency, RGW request latency, and RBD image performance. Metrics must be interpreted by workload and failure state.

### Deep technical explanation

- Averages hide tail latency, which often drives application timeouts.
- OSD latency should be correlated with underlying device and replica-path latency.
- Cluster throughput can look healthy while one pool or client experiences hot-PG contention.
- Recovery bandwidth and foreground latency must be monitored together.
- Capacity metrics need trend and failure-state projection.

### Useful commands

```bash
ceph osd perf
ceph pg stat
ceph df detail
ceph tell mgr dump_osd_network 0
ceph daemon osd.<id> perf dump
```


### L3 validation points

- Use p95 and p99 latency.
- Tag metrics by pool, host, device class, and service.
- Keep a baseline from healthy periods.

### Common mistake

Using total cluster IOPS as the only performance indicator.

### Strong L3 closing statement

> I monitor the bottleneck chain and tail latency, not just aggregate throughput.


## Q3. How do Prometheus, Alertmanager, Grafana, and node exporters integrate with Ceph?

### Interview answer

The Ceph manager Prometheus module exports cluster metrics. Cephadm can deploy Prometheus, Alertmanager, Grafana, and node-exporter services. Prometheus stores time series, Alertmanager routes alerts, Grafana visualizes dashboards, and node-exporter provides host metrics. Monitoring components must themselves be redundant, secured, retained, and capacity-managed.

### Deep technical explanation

- Ceph metrics should be joined with host, switch, application, and client telemetry.
- Alert rules need business impact and runbook links, not only component states.
- Monitoring outage during a storage incident is a known failure mode; place services across hosts and preserve external alerting paths where possible.
- Long retention and high-cardinality labels can consume significant storage.

### Useful commands

```bash
ceph mgr module enable prometheus
ceph mgr services
ceph orch ls --service_type prometheus
ceph orch ls --service_type grafana
ceph orch ps --daemon-type alertmanager
```


### L3 validation points

- Test alert delivery.
- Protect dashboards and metrics endpoints.
- Monitor the monitoring stack.

### Common mistake

Deploying default dashboards but not defining actionable alerts and ownership.

### Strong L3 closing statement

> Observability is an operational service with its own HA, security, and capacity requirements.


## Q4. How do you calculate usable capacity for replicated and EC pools?

### Interview answer

Start with raw device capacity, subtract unavailable or reserved devices and BlueStore/operational overhead, apply replication or erasure-coding efficiency, then preserve safety headroom for imbalance and failure recovery. Capacity must be modeled per device class and CRUSH root, not only cluster-wide.

### Deep technical explanation

- For three-copy replication, theoretical logical capacity is raw divided by three before reserve.
- For EC `k+m`, theoretical data efficiency is `k/(k+m)` before reserve.
- The fullest OSD and the loss of a host or rack may determine usable capacity earlier than total raw capacity.
- Snapshot, versioning, clones, deleted-but-retained data, and recovery overhead must be included.
- Procurement lead time and rebalance duration define the expansion trigger.

### Useful commands

```bash
ceph df detail
ceph osd df tree
ceph osd crush tree
rbd du <pool>/<image>
radosgw-admin bucket stats --bucket=<bucket>
```


### L3 validation points

- Model N+1 or required failure state.
- Track growth by service class.
- Use the most utilized OSD.

### Common mistake

Quoting theoretical EC or replica efficiency as safe sellable capacity.

### Strong L3 closing statement

> Usable capacity is the capacity that remains safe and operable after the defined failure, not the best-case arithmetic maximum.


## Q5. How do you benchmark Ceph correctly?

### Interview answer

Define the application I/O profile, test at the correct layer, isolate test data, warm up, run long enough to reach steady state, capture tail latency and resource metrics, and include degraded and recovery scenarios. Use `rados bench`, RBD fio, CephFS tools, or S3 benchmarks only for the layer they represent. Never run destructive tests on production data.

### Deep technical explanation

- `rados bench` measures RADOS object behavior and does not model filesystem, database, or S3 semantics.
- Fio parameters must match block size, read/write ratio, queue depth, direct I/O, flush behavior, and working-set size.
- Cache effects can make short tests misleading.
- Benchmark the expected concurrency and client distribution.
- Record software, firmware, configuration, topology, and test data so results are reproducible.

### Useful commands

```bash
rados bench -p <test-pool> 300 write --no-cleanup
rados bench -p <test-pool> 300 rand
fio --name=rbdtest --filename=rbd:<pool>/<image> --rw=randrw --rwmixread=70 --bs=4k --iodepth=32 --runtime=300 --time_based
```


### L3 validation points

- Use dedicated test pools/images.
- Capture system metrics.
- Clean test objects safely.

### Common mistake

Comparing cached one-minute benchmarks from different environments.

### Strong L3 closing statement

> A benchmark is valid only when it is reproducible, workload-representative, and measured with the full resource context.


## Q6. How do recovery and backfill affect client performance?

### Interview answer

Recovery and backfill consume disk, CPU, and network resources on source and destination OSDs. Increasing recovery concurrency can restore redundancy faster but may increase foreground latency. The correct balance depends on current risk, client SLOs, failure scope, and time to recover.

### Deep technical explanation

- Recovery from one failed OSD differs from rebuilding a full host or rack.
- Backfill may move large object sets to new placement, while recovery reconstructs missing replicas or shards.
- Erasure-coded recovery can consume additional CPU and network.
- Use scheduled policies or QoS controls supported by the release, and monitor before and after changes.
- Never leave emergency recovery overrides undocumented.

### Useful commands

```bash
ceph -w
ceph progress
ceph pg stat
ceph config get osd osd_max_backfills
ceph config get osd osd_recovery_max_active
```


### L3 validation points

- Track client p99 latency.
- Estimate time at current recovery rate.
- Preserve capacity headroom.

### Common mistake

Maximizing recovery threads because the cluster is degraded, causing an application outage.

### Strong L3 closing statement

> I tune recovery from a risk trade-off: time exposed to reduced redundancy versus immediate client impact.


## Q7. How do you identify a hot OSD, hot PG, or hot object?

### Interview answer

Compare per-OSD utilization, IOPS, latency, network, and CPU. Map affected client objects or images to PGs and acting sets. Check whether a small number of PGs or objects dominate traffic, whether the primary role is uneven, and whether application key or image layout creates skew. Balancing capacity does not necessarily balance reads or hot-object access.

### Deep technical explanation

- Read-heavy workloads can overload primary OSDs even when data capacity is balanced.
- The read balancer or primary affinity mechanisms may help in supported designs, but application sharding is often the durable solution.
- A single popular RGW object or RBD image can remain hot regardless of PG count.
- Compare network and disk counters to distinguish read, write, and replication hotspots.

### Useful commands

```bash
ceph osd perf
ceph osd df tree
ceph pg dump pgs_brief
ceph osd map <pool> <object>
rbd perf image iostat
```


### L3 validation points

- Separate capacity skew from workload skew.
- Identify primary OSD concentration.
- Validate application access patterns.

### Common mistake

Adding PGs or reweighting OSDs without proving that data distribution is the cause.

### Strong L3 closing statement

> Hotspots are workload-placement problems; I trace the dominant object or PG before applying balancing tools.


## Q8. What is a good Ceph capacity-planning process?

### Interview answer

Collect growth and peak data by pool and device class, model replica or EC overhead, retention, snapshots, versioning, failure-state headroom, and expansion lead time. Forecast when the fullest failure domain will cross operational thresholds, not when cluster raw capacity reaches 100 percent. Review quarterly or more often for fast-growing services.

### Deep technical explanation

- Include replacement capacity for failed and aging devices.
- Model a host or rack loss during the forecast period.
- Use separate growth rates for RBD, CephFS metadata, RGW data, and index pools.
- Procurement, installation, burn-in, and rebalance can take weeks.
- Track performance capacity as well as bytes; IOPS or network may exhaust first.

### Useful commands

```bash
ceph df detail --format json
ceph osd df tree --format json
ceph osd pool autoscale-status
ceph device ls
```


### L3 validation points

- Use trend confidence ranges.
- Include N+1 headroom.
- Trigger expansion before nearfull.

### Common mistake

Planning only on average monthly raw-capacity growth.

### Strong L3 closing statement

> Ceph capacity planning predicts the first constrained resource under a defined failure, not only total bytes consumed.



---

# Troubleshooting, Upgrades, and L3 Scenarios

## Q1. How do you troubleshoot a cluster in HEALTH_WARN?

### Interview answer

List every health check, classify each by data availability, durability, capacity, performance, security, or lifecycle risk, and address root causes in priority order. Determine whether warnings are expected temporary states, symptoms of a common failure, or independent problems. Do not mute warnings until their impact and remediation are documented.

### Deep technical explanation

- Examples include old daemon versions, down MONs, degraded PGs, nearfull OSDs, overdue scrub, crash records, insecure configuration, and insufficient standby daemons.
- One hardware or network failure can generate many related warnings.
- Use the health-check identifier for automation and runbook mapping.
- Sticky mutes can conceal future incidents and must have expiry and owner.

### Useful commands

```bash
ceph health detail
ceph health mute <check-id> --sticky
ceph health unmute <check-id>
ceph crash ls-new
ceph -w
```


### L3 validation points

- State user impact.
- Group symptoms by root cause.
- Verify warning clearance after remediation.

### Common mistake

Muting a warning to make the dashboard green.

### Strong L3 closing statement

> Health state is a prioritized risk list; I convert each check into impact, owner, action, and verification.


## Q2. A host with twelve OSDs fails. What is your response?

### Interview answer

Confirm the host failure and application impact, preserve evidence, check whether MON, MGR, MDS, RGW, or other services were also on the host, and verify PG availability and remaining capacity. Decide whether the host will return within the safe maintenance window. Avoid premature mass replacement or aggressive recovery. If the host is permanently lost, evacuate or replace in controlled stages with failure-domain and capacity checks.

### Deep technical explanation

- Check for inactive PGs, reduced replica count, and whether another host failure would cause outage.
- Investigate power, network, kernel, hardware management, and storage fabric evidence.
- `noout` may be appropriate for a short known outage but dangerous for an uncertain or long failure.
- Recovery of twelve OSDs can saturate the cluster; protect foreground SLOs.
- After restoration, verify data convergence and underlying host health before full reintegration.

### Useful commands

```bash
ceph -s
ceph health detail
ceph orch ps --host <host>
ceph osd tree
ceph osd df tree
ceph orch host ok-to-stop <host>
```


### L3 validation points

- Assess second-failure risk.
- Set a decision deadline for return versus rebuild.
- Monitor recovery and client latency.

### Common mistake

Immediately marking every OSD out without checking whether the host will return quickly or whether the cluster has recovery capacity.

### Strong L3 closing statement

> A failed OSD host is a failure-domain incident; I stabilize availability, set a time-bounded recovery strategy, and control data movement.


## Q3. How do you handle inactive or peering PGs?

### Interview answer

Identify affected PGs, acting and up sets, missing or down OSDs, last intervals, blocked-by dependencies, and available histories. Restore connectivity or the missing OSDs where possible. Only use advanced PG recovery or marking-lost procedures after establishing the authoritative data history and accepting documented data-loss risk.

### Deep technical explanation

- Peering protects consistency by preventing unsafe I/O until OSDs agree on history.
- Network partitions, clock or map issues, full OSDs, and missing OSD histories are common causes.
- `ceph pg query` provides detailed state transitions and recovery blockers.
- Destructive commands can permanently discard the only current copy.

### Useful commands

```bash
ceph pg dump_stuck inactive
ceph pg <pgid> query
ceph pg map <pgid>
ceph osd blocked-by
ceph osd tree
```


### L3 validation points

- Find last known complete interval.
- Restore missing OSDs before declaring data lost.
- Escalate destructive action.

### Common mistake

Using `mark_unfound_lost revert/delete` without a validated data-loss decision.

### Strong L3 closing statement

> Peering is a safety mechanism; I restore authoritative history rather than bypassing it.


## Q4. How do you troubleshoot a nearfull or full cluster?

### Interview answer

Stop uncontrolled growth, identify the fullest OSDs and affected pools, check CRUSH and weight skew, remove safe temporary data, add capacity, and restore healthy distribution. Avoid simply raising thresholds. If writes are blocked, prioritize data safety and business-approved emergency actions while preserving enough space for recovery.

### Deep technical explanation

- Fullness may be caused by one pool, one device class, an incorrect CRUSH rule, a failed OSD, snapshots, RGW versions, or imbalanced weights.
- Deleting data may not free space immediately due to snapshots, trash, versioning, or recovery.
- Adding capacity also requires free resources for rebalance.
- Do not perform mass OSD reweights that produce uncontrolled movement.

### Useful commands

```bash
ceph df detail
ceph osd df tree
ceph health detail
rbd trash ls <pool>
radosgw-admin gc list --include-all
```


### L3 validation points

- Identify reclaimable versus protected data.
- Model post-failure headroom.
- Monitor data movement.

### Common mistake

Increasing `mon_osd_full_ratio` as the standard fix.

### Strong L3 closing statement

> Fullness is a capacity-governance failure; emergency changes must buy time for real reclamation or expansion without weakening data safety.


## Q5. What is your process for a rolling RHCS upgrade?

### Interview answer

Validate the supported source-to-target path and compatibility matrix, review release notes and known issues, update backups and recovery evidence, confirm cluster health and capacity, stage images and registry access, pause risky background changes, then use cephadm’s orchestrated upgrade. Monitor daemon versions, health, PGs, client SLOs, and upgrade progress. Stop or roll back according to documented abort criteria.

### Deep technical explanation

- Cephadm follows a safe daemon order and checks availability before restarts.
- Mixed versions are temporary and expected, but prolonged mixed-version state should be investigated.
- Client compatibility and minimum required releases must be verified before finalizing feature changes.
- Do not combine major topology, kernel, firmware, and Ceph upgrades in one change.
- Post-upgrade validation includes scrub, failover, monitoring, dashboard, gateways, and application tests.

### Useful commands

```bash
ceph versions
ceph orch upgrade check <image>
ceph orch upgrade start --image <image>
ceph orch upgrade status
ceph orch upgrade stop
```


### L3 validation points

- Use exact Red Hat-supported images.
- Define abort criteria.
- Validate applications after each stage.

### Common mistake

Upgrading an unhealthy or nearfull cluster because the new release might fix it.

### Strong L3 closing statement

> An upgrade is a controlled availability event; I enter with a healthy cluster, supported path, staged evidence, and tested rollback or pause procedures.


## Q6. How do you design backup and disaster recovery for Ceph?

### Interview answer

Match protection to each interface. RBD can use snapshots, external backup tooling, and asynchronous mirroring. CephFS can use snapshots, file-level backup, and snapshot mirroring where supported. RGW can use versioning, lifecycle, multisite, and independent object backup. Cluster configuration, keys, CRUSH maps, and service specifications also need protected copies. Recovery must be tested to application level.

### Deep technical explanation

- Replication and mirroring improve availability or RPO but do not protect against authorized deletion, corruption, or compromised administration.
- Backups should reside in a separate failure and security domain.
- Define RPO, RTO, retention, encryption, and restore ownership per service.
- Back up enough control-plane metadata to rebuild, but do not assume restoring monitor databases is the normal way to recover user data.
- Run DR exercises that include DNS, credentials, clients, and validation.

### Useful commands

```bash
ceph orch ls --export > cluster-spec.yaml
ceph config dump > ceph-config.txt
ceph osd getcrushmap -o crushmap.bin
rbd mirror pool status <pool> --verbose
radosgw-admin sync status
```


### L3 validation points

- Test restore, not only backup completion.
- Separate backup credentials.
- Document application consistency.

### Common mistake

Calling a second copy in the same cluster a disaster-recovery solution.

### Strong L3 closing statement

> My DR design protects data, configuration, identity, and client cutover in independent domains, with measured RPO and RTO.


## Q7. An application reports latency, but `ceph -s` is HEALTH_OK. What do you do?

### Interview answer

Treat `HEALTH_OK` as only a cluster-state signal. Measure application latency and error rates, then trace the path through client queues, network, protocol gateway, pool and PG mapping, primary and replica OSDs, devices, CPU, and recovery or scrub activity. Compare affected and healthy clients and correlate timestamps with workload and change events.

### Deep technical explanation

- Ceph health checks do not detect every performance regression.
- Tail latency may be caused by a few slow OSDs while aggregate health remains normal.
- Client-side CPU, DNS, TLS, cache, kernel, hypervisor, or mount settings may be the bottleneck.
- Use application SLO evidence to guide the investigation.

### Useful commands

```bash
ceph osd perf
ceph pg stat
ceph -w
iostat -xz 1
sar -n DEV,TCP,ETCP 1
```


### L3 validation points

- Capture p95/p99 latency.
- Map affected data to acting sets.
- Check recent changes.

### Common mistake

Telling the application team that storage is healthy solely because the dashboard is green.

### Strong L3 closing statement

> Cluster health and service performance are different dimensions; I validate the user path against the application SLO.


## Q8. Describe an L3 root-cause-analysis report for a Ceph incident.

### Interview answer

The report should include executive impact, timeline, detection, affected services and data, technical root cause, contributing factors, evidence, mitigation, recovery, data-integrity assessment, corrective actions, preventive actions, owners, deadlines, and validation. It must distinguish root cause from symptoms and explain why existing controls did not prevent or detect the incident earlier.

### Deep technical explanation

- Include exact map epochs, daemon events, PG states, hardware logs, change records, and application metrics.
- Quantify RPO, RTO, error rate, latency, and durability exposure.
- Corrective actions address the immediate defect; preventive actions improve architecture, monitoring, process, and testing.
- Do not blame individuals; identify control and system weaknesses.
- Verify action effectiveness through drills or measurable thresholds.

### Useful commands

```bash
ceph report > incident-ceph-report.json
ceph health detail > incident-health.txt
ceph log last 500 > incident-clog.txt
journalctl --since '<start>' --until '<end>' > incident-journal.txt
```


### L3 validation points

- Use a single synchronized timeline.
- Attach evidence and change IDs.
- Assign measurable prevention actions.

### Common mistake

Writing 'OSD failed' as the root cause when the underlying cause was firmware, power, network, or operational process.

### Strong L3 closing statement

> A strong RCA explains the causal chain, control gaps, and verified prevention—not just the component that surfaced the alert.



---

## Official reference baseline

- Red Hat Ceph Storage 9 documentation: https://docs.redhat.com/en/documentation/red_hat_ceph_storage/9
- Red Hat Ceph Storage life cycle: https://access.redhat.com/support/policy/updates/ceph-storage
- Red Hat Ceph Storage 8 Operations Guide: https://docs.redhat.com/en/documentation/red_hat_ceph_storage/8/html-single/operations_guide/operations_guide
- Red Hat Ceph Storage 8 Architecture Guide: https://docs.redhat.com/en/documentation/red_hat_ceph_storage/8/pdf/architecture_guide/Red_Hat_Ceph_Storage-8-Architecture_Guide-en-US.pdf
- Upstream Ceph architecture: https://docs.ceph.com/en/latest/architecture/
- Upstream Ceph health checks: https://docs.ceph.com/en/latest/rados/operations/health-checks/
- Upstream Ceph BlueStore configuration reference: https://docs.ceph.com/en/latest/rados/configuration/bluestore-config-ref/
- Upstream Ceph RBD mirroring: https://docs.ceph.com/en/latest/rbd/rbd-mirroring/

> Upstream “latest” pages can describe development behavior. For production decisions, prefer the Red Hat documentation for the exact supported RHCS release.
