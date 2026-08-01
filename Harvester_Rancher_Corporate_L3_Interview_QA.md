# Harvester and Rancher — Corporate L3 Interview Questions and Detailed Answers

> **Audience:** Senior Linux, Kubernetes, virtualization, platform, cloud, SRE, and infrastructure engineers with production responsibilities.  
> **Level:** Corporate L3 / senior administrator / platform engineer.  
> **Documentation snapshot:** 1 August 2026. Examples align with current Rancher Manager 2.14 and Harvester/SUSE Virtualization 1.8 documentation, but commands and upgrade paths must always be validated against the exact supported-version matrix used by your organization.

---

## Table of Contents

1. [Platform Fundamentals and Architecture](#1-platform-fundamentals-and-architecture)
2. [Production Design and Installation](#2-production-design-and-installation)
3. [Harvester Compute and VM Lifecycle](#3-harvester-compute-and-vm-lifecycle)
4. [Harvester Networking](#4-harvester-networking)
5. [Harvester Storage, Backup, and DR](#5-harvester-storage-backup-and-dr)
6. [Rancher Multi-Cluster Administration](#6-rancher-multi-cluster-administration)
7. [Security, Governance, and Enterprise Operations](#7-security-governance-and-enterprise-operations)
8. [Rancher–Harvester Integration](#8-rancherharvester-integration)
9. [Troubleshooting and Corporate Scenarios](#9-troubleshooting-and-corporate-scenarios)
10. [Command Reference and Interview Checklist](#10-command-reference-and-interview-checklist)
11. [Official References](#11-official-references)

---

# 1. Platform Fundamentals and Architecture

## Q1. What is Harvester, and where does it fit in an enterprise infrastructure stack?

### Detailed answer

Harvester is a Kubernetes-native hyperconverged infrastructure platform designed to run virtual machines on bare-metal servers. It combines:

- **KubeVirt** for virtual-machine lifecycle management.
- **Kubernetes/RKE2** as the control and orchestration layer.
- **Longhorn** for distributed block storage.
- **Multus and bridge CNI components** for attaching VMs to multiple networks.
- **Rancher integration** for centralized lifecycle and access management.

From an enterprise architecture viewpoint, Harvester is not simply a GUI around KVM. A VM is represented by Kubernetes custom resources, and its running instance is hosted inside a `virt-launcher` pod. This means scheduling, admission control, reconciliation, RBAC, observability, and declarative APIs are Kubernetes-driven.

A typical placement is:

```text
Physical servers
  └─ Harvester OS + RKE2
      ├─ KubeVirt compute
      ├─ Longhorn distributed storage
      ├─ Harvester networking
      └─ Virtual machines
          └─ RKE2/K3s or conventional applications
```

Harvester is suitable when an organization wants to consolidate traditional VMs and cloud-native clusters on a modern API-driven platform, especially at branch, edge, private-cloud, lab, or data-center locations.

### L3 considerations

A senior engineer must evaluate failure domains, quorum, storage replica placement, NIC separation, supported hardware, backup targets, upgrade paths, and capacity headroom. Installing three nodes is not by itself a production design; the design must survive node maintenance, disk failure, network partition, and upgrade operations without violating workload service-level objectives.

---

## Q2. What is Rancher Manager, and how is it different from Kubernetes itself?

### Detailed answer

Kubernetes is the workload orchestration platform. Rancher Manager is an enterprise management plane placed above one or more Kubernetes clusters. Rancher provides:

- Cluster provisioning and import.
- Centralized authentication and authorization.
- Cluster and project abstractions.
- Policy and configuration management.
- Application catalog and Helm-based deployment.
- Fleet-based continuous delivery and GitOps.
- Monitoring, logging, security, and ecosystem integrations.
- Central access to downstream clusters.

Rancher does not replace each downstream cluster's API server, scheduler, controllers, or etcd. Each downstream cluster remains operational even when Rancher is temporarily unavailable, although central management functions and proxied access may be affected.

The Rancher management cluster is called the **local cluster**. Managed workload clusters are commonly called **downstream clusters**.

### Corporate design rule

For production, Rancher should normally run on a dedicated highly available Kubernetes cluster. User workloads should not be placed on the Rancher management cluster because resource contention, security exposure, or workload incidents could affect the organization's ability to manage all downstream clusters.

---

## Q3. Explain the major Harvester architecture components.

### Detailed answer

The important components are:

1. **RKE2/Kubernetes control plane**  
   Maintains cluster state, schedules system workloads, exposes APIs, and hosts Harvester custom resources.

2. **KubeVirt**  
   Extends Kubernetes with VM resources. `virt-controller`, `virt-handler`, `virt-api`, and `virt-launcher` participate in VM scheduling and execution.

3. **QEMU/KVM and libvirt-related runtime functionality**  
   Provides hardware-assisted virtualization on each compute node.

4. **Longhorn**  
   Provides replicated distributed block volumes used as VM disks. Longhorn manages engines, replicas, attachment, snapshots, backups, and rebuilds.

5. **Container networking and VM networking**  
   The management network uses the cluster CNI, while VLAN or untagged VM networks use Multus and bridge-based network attachments. Newer deployments may also use overlay/VPC capabilities where supported.

6. **Harvester controllers and UI**  
   Provide higher-level VM, image, network, host, upgrade, backup, and settings workflows.

7. **Embedded Rancher capabilities**  
   Harvester contains Rancher components for managing the local platform. This embedded instance must not be confused with an external, enterprise Rancher Manager used to manage multiple clusters.

8. **Elemental/immutable operating-system lifecycle components**  
   The node operating system is appliance-like and upgraded as an integrated platform rather than managed as a general-purpose Linux server.

### Key L3 implication

Troubleshooting requires moving across layers:

```text
VM guest → KubeVirt → Kubernetes pod/scheduling → node OS/KVM
        → CNI/bridge/VLAN → Longhorn volume → physical NIC/disk/network
```

Stopping at the UI is not enough. An L3 engineer must correlate Kubernetes objects, KubeVirt status, Longhorn health, host resources, and physical infrastructure.

---

## Q4. Explain how a Harvester virtual machine runs internally.

### Detailed answer

A Harvester VM is represented by a KubeVirt `VirtualMachine` object. When started, KubeVirt creates a `VirtualMachineInstance` and schedules a `virt-launcher` pod on an eligible node. The QEMU process that executes the guest runs within this pod's context while KubeVirt controllers continuously reconcile the desired state.

Simplified flow:

```text
VirtualMachine desired state: running
        ↓
VirtualMachineInstance created
        ↓
Kubernetes scheduler selects a node
        ↓
virt-launcher pod starts
        ↓
Volumes and networks are attached
        ↓
QEMU/KVM starts guest OS
```

Useful commands:

```bash
kubectl get virtualmachines -A
kubectl get virtualmachineinstances -A
kubectl get pods -A -l kubevirt.io=virt-launcher -o wide
kubectl describe virtualmachine <vm-name> -n <namespace>
kubectl describe virtualmachineinstance <vm-name> -n <namespace>
```

### Interview depth

A VM can fail to start even when the VM YAML is valid. Typical causes include:

- No node satisfies CPU, memory, affinity, or network constraints.
- PVC or Longhorn volume is not ready or cannot attach.
- Required network attachment is unavailable on eligible nodes.
- Image import is incomplete.
- Admission webhook rejects the configuration.
- KubeVirt or node virtualization capability is unhealthy.
- Node has insufficient allocatable resources despite apparently free host memory.

---

## Q5. What is hyperconverged infrastructure, and what are its advantages and risks in Harvester?

### Detailed answer

Hyperconverged infrastructure combines compute, storage, and platform services on the same cluster nodes. In Harvester, the same nodes may run VMs and Longhorn replicas.

### Advantages

- Simplified scale-out model.
- Commodity-server utilization.
- Unified lifecycle and management.
- Locality can provide low-latency storage access.
- Kubernetes-native automation and APIs.
- Reduced dependence on a separate SAN for many workloads.

### Risks

- Compute and storage compete for CPU, memory, network, and I/O.
- A node failure removes both compute capacity and storage replicas.
- Rebuild traffic can interfere with production VM traffic.
- Poor capacity planning can make maintenance impossible.
- Large failure domains may trigger simultaneous VM rescheduling and storage rebuild.
- Oversubscription can be dangerous because control-plane, storage, and workload resources share the same hosts.

### L3 recommendation

Reserve explicit headroom for:

- One-node failure.
- Longhorn replica rebuild.
- VM live migration.
- Rolling upgrades.
- Control-plane and system pods.
- Traffic bursts and backup windows.

A design that uses 80–90% of memory or disk under normal conditions often cannot safely tolerate an upgrade or failed node.

---

## Q6. What is the difference between the Rancher local cluster and downstream clusters?

### Detailed answer

The **local cluster** is the Kubernetes cluster where Rancher itself is installed. It hosts Rancher server pods and Rancher-specific state. A **downstream cluster** is a Kubernetes cluster created, imported, or managed through Rancher.

Important distinctions:

| Area | Local cluster | Downstream cluster |
|---|---|---|
| Purpose | Hosts Rancher management plane | Runs applications or platform services |
| Failure impact | Central management/UI/API affected | Applications in that cluster affected |
| Backup | Rancher backup operator and management-cluster backup process | Distribution-specific etcd and application backup |
| Access | Rancher administrators | Cluster/project users and workload teams |
| Upgrade | Rancher and management Kubernetes lifecycle | Cluster-specific Kubernetes lifecycle |

Rancher outage generally does not stop already-running workloads in downstream clusters. However, centralized authentication, UI access, cluster provisioning, Fleet synchronization, and some Rancher-managed operations can be impaired.

---

## Q7. Why should Rancher be installed on a dedicated HA Kubernetes cluster?

### Detailed answer

A dedicated HA installation isolates the management plane from user workload incidents. It also provides multiple Rancher replicas, resilient Kubernetes control-plane services, and a load-balanced endpoint.

Production architecture commonly includes:

```text
Users / automation
       ↓
Enterprise load balancer / DNS
       ↓
Rancher ingress on 3-node management cluster
       ↓
Rancher replicas + cert-manager + system services
       ↓
Downstream clusters
```

Benefits include:

- No single management-server failure.
- Better change control and capacity isolation.
- Reduced blast radius.
- Easier backup and restore.
- Independent maintenance schedule.
- Clear firewall and access boundaries.

### What HA does not solve

HA Rancher does not replace backups. A replicated bad configuration, accidental deletion, certificate error, or failed upgrade can affect all replicas. You still need tested Rancher backups, etcd protection for the management cluster, DNS/LB resilience, and documented recovery procedures.

---

## Q8. What is the role of etcd in Rancher and Harvester?

### Detailed answer

etcd stores Kubernetes control-plane state. In Rancher’s local cluster, Rancher custom resources, secrets, cluster metadata, and system objects ultimately reside in Kubernetes/etcd. In Harvester, VM definitions, KubeVirt resources, Longhorn metadata, networking objects, settings, and platform state are represented through Kubernetes resources and therefore depend on a healthy control plane.

### Important distinction

etcd does not contain the full contents of VM disks. Longhorn stores VM block data on node disks. Consequently, a complete recovery strategy must separately address:

- Kubernetes/management-plane state.
- Rancher application backup.
- Harvester/VM data backups.
- External images, registries, DNS, certificates, and object storage.

### L3 health checks

```bash
kubectl get nodes
kubectl get --raw='/readyz?verbose'
kubectl get pods -n kube-system -o wide
journalctl -u rke2-server --since '30 min ago'
```

Do not run unsupported manual etcd commands or delete etcd data during incident response. Follow the product-supported backup, snapshot, and restore workflow for the exact release.

---

# 2. Production Design and Installation

## Q9. What hardware requirements must be evaluated before installing Harvester?

### Detailed answer

A corporate design must validate more than minimum CPU and RAM. Review:

- 64-bit x86 servers supported by the chosen release.
- Hardware virtualization extensions enabled in BIOS/UEFI.
- UEFI/boot mode requirements for current and future upgrades.
- Sufficient CPU cores and memory for system services plus VMs.
- Dedicated installation disk and suitable data disks.
- Enterprise SSD/NVMe endurance and consistent performance.
- NIC count, speed, offload support, bonding, VLAN trunking, and MTU.
- RAID/HBA mode compatibility with Longhorn’s direct-disk expectations.
- Time synchronization, DNS, DHCP/static addressing, and BMC access.
- Supported firmware, drivers, and hardware compatibility.
- Failure-domain separation across racks, power, switches, and storage media.

### Practical production baseline

For a three-node cluster, calculate whether two remaining nodes can host critical VMs after one node fails. Also confirm whether surviving disks have enough free capacity to rebuild Longhorn replicas. CPU or memory sizing alone is insufficient.

### Common design mistake

Using one small boot disk and one nearly full data disk per node may pass installation but fail during image imports, snapshots, replica rebuilds, logging spikes, or upgrades. Capacity thresholds and system partition free space must be monitored proactively.

---

## Q10. How would you design a production Harvester network?

### Detailed answer

A mature design separates traffic classes where scale and hardware permit:

1. **Management/control-plane network** for node administration and Kubernetes traffic.
2. **VM production networks** using VLAN or untagged bridge networks.
3. **Storage network** for Longhorn replication and data-path traffic.
4. **VM migration network** where supported and justified.
5. **Out-of-band management network** for BMC/IPMI/Redfish.
6. **Backup network** if backup throughput could interfere with production.

Example:

```text
NIC1 + NIC2 → bonded management/control plane
NIC3 + NIC4 → bonded VM VLAN trunk
NIC5 + NIC6 → bonded storage/migration network
BMC NIC      → isolated OOB network
```

### Key checks

- Configure switch-side LACP or static bonding consistently with host mode.
- Validate allowed VLANs end to end.
- Keep MTU consistent across NIC, bond, bridge, switch, router, and guest.
- Avoid asymmetric routing.
- Reserve adequate IP addresses for system services, upgrades, RWX volumes, and load balancers.
- Confirm all Harvester nodes can reach Rancher’s load-balancer endpoint on TCP 443 when integrating the platforms.

---

## Q11. How do you choose the number of Harvester nodes?

### Detailed answer

The minimum node count is not the same as the recommended production count. Node count should follow workload availability, quorum, maintenance, storage replica policy, and performance requirements.

### Three-node design

- Common starting point.
- Can tolerate one management-node failure if quorum remains.
- Tight capacity during maintenance.
- Replica rebuild and VM migration headroom may be limited.

### Four or more nodes

- Better compute and storage flexibility.
- More placement choices.
- Easier rolling maintenance.
- Must still consider quorum and failure-domain distribution.

### Witness-node concept

Where the release supports it, a witness may maintain consensus without running workloads or storing VM data. It is not a substitute for compute or storage capacity.

### Capacity rule

Size the cluster so that after the largest planned failure domain is removed, the remaining cluster can run protected workloads and maintain healthy storage replicas. Use actual workload reservations and observed peaks rather than host-level average utilization.

---

## Q12. Explain the major Harvester installation methods.

### Detailed answer

Common methods include:

- Interactive ISO installation.
- Net-install ISO.
- PXE/iPXE automated installation.
- Automated installation using configuration files and metadata mechanisms.
- Release-specific binary or provisioning modes for specialized bare-metal/edge workflows.

### Corporate decision factors

| Method | Suitable for | Main concerns |
|---|---|---|
| Interactive ISO | Small lab or first validation | Manual drift and slower scaling |
| Net install | Controlled network installation | Repository availability and trust |
| PXE/iPXE | Repeatable fleet deployment | DHCP/TFTP/HTTP design and secrets |
| Automated config | Enterprise standardization | Schema/version compatibility |

An L3 engineer should keep installation configuration under version control, remove plaintext secrets, validate checksums/signatures, standardize BIOS and firmware settings, and test reinstall/join procedures before production rollout.

---

## Q13. What must be validated before joining an additional Harvester node?

### Detailed answer

Validate:

- Exact or supported platform version.
- DNS and time synchronization.
- Management IP uniqueness.
- Token validity and secure handling.
- Layer-2 and layer-3 reachability.
- MTU and VLAN consistency.
- Hostname uniqueness.
- Disk cleanliness and intended role.
- CPU virtualization support.
- Required ports and firewall rules.
- NIC naming consistency if automation depends on interface names.
- Sufficient cluster health before expansion.

After joining:

```bash
kubectl get nodes -o wide
kubectl get pods -A -o wide | grep <new-node>
kubectl get virtualmachineinstances -A -o wide
kubectl -n longhorn-system get pods -o wide
```

Then verify Longhorn disk discovery, node scheduling eligibility, network configuration, labels, taints, and the ability to place a non-critical test VM before allowing production workloads.

---

## Q14. How would you install Rancher Manager for production?

### Detailed answer

A production pattern is:

1. Build a dedicated supported Kubernetes cluster, commonly RKE2 or K3s depending on the support and security requirements.
2. Place at least three management/control-plane nodes across failure domains.
3. Provide stable DNS and a highly available load balancer.
4. Install cert-manager when using the relevant certificate workflow.
5. Install Rancher using the supported Helm chart repository and pinned version.
6. Configure TLS, ingress, replicas, private CA trust, proxy settings, audit logging, and resource requests.
7. Validate backup operator, monitoring, authentication, and downstream-cluster connectivity.

Example outline:

```bash
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
helm repo update

helm upgrade --install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --create-namespace \
  --set hostname=rancher.example.com \
  --set replicas=3 \
  --version <approved-version>
```

The exact values depend on whether TLS is terminated at Rancher ingress or an external load balancer, whether a private CA is used, and whether outbound internet access requires a proxy.

### L3 rule

Never deploy an unpinned “latest” chart directly into production. Maintain tested Helm values, a compatibility matrix, pre-upgrade backup, rollback procedure, and a non-production rehearsal environment.

---

## Q15. What is the purpose of the Rancher load balancer, and what health checks should be configured?

### Detailed answer

The load balancer provides a stable endpoint for users, agents, APIs, and downstream clusters. It distributes connections across Rancher ingress endpoints or management nodes, depending on the architecture.

Requirements include:

- Stable DNS record.
- TCP 443 reachability from administrators, downstream clusters, and Harvester nodes.
- Correct TLS passthrough or termination design.
- WebSocket support.
- Suitable idle timeouts for long-running connections.
- Health checks that reflect ingress/Rancher availability.
- Preservation of source information where needed.

### Failure symptoms

Misconfigured load balancers can cause:

- UI intermittently failing.
- Cluster agents flapping between connected and disconnected.
- WebSocket errors.
- Certificate mismatch.
- Redirect loops.
- Large uploads or Helm operations failing.
- Login callbacks failing with external identity providers.

### Troubleshooting

```bash
curl -vk https://rancher.example.com/ping
openssl s_client -connect rancher.example.com:443 -servername rancher.example.com
kubectl -n cattle-system get pods -o wide
kubectl -n cattle-system logs deploy/rancher --since=20m
```

Test from both an administrator workstation and a downstream-cluster node because routing, DNS, proxy, or CA trust may differ.

---

## Q16. How do you plan an air-gapped Harvester and Rancher deployment?

### Detailed answer

An air-gapped deployment requires an internal registry and a controlled artifact-import process. The plan must include:

- Product images for Rancher, Harvester, RKE2/K3s, system charts, and dependencies.
- Helm charts and CRDs.
- Harvester ISO and upgrade artifacts.
- OS images used by VMs.
- Internal CA certificates.
- DNS and NTP available inside the restricted zone.
- Vulnerability scanning and artifact approval.
- Registry high availability, storage, backup, and retention.
- Proxy or no-proxy values where limited egress exists.
- A repeatable process for every patch release.

### Validation checklist

```bash
# Check whether system pods are blocked by missing images
kubectl get pods -A | egrep 'ImagePullBackOff|ErrImagePull'

# Inspect a failed pull
kubectl describe pod <pod> -n <namespace>

# Confirm node-side registry and CA configuration
journalctl -u rke2-server --since '30 min ago'
journalctl -u rke2-agent --since '30 min ago'
```

The most common air-gap failure is incomplete image mirroring. A second common failure is that images exist but are stored under a different repository path or are signed/trusted differently from what the platform expects.

---

# 3. Harvester Compute and VM Lifecycle

## Q17. How are VM images managed in Harvester?

### Detailed answer

Harvester imports VM images into the platform so they can be used to create virtual disks. Image sources may include HTTP endpoints, uploaded files, or supported registry-like workflows depending on the release.

Important operational points:

- An image is not ready until the import process completes.
- Image data consumes Longhorn/storage capacity.
- Large images can generate considerable network and disk traffic.
- Image checksums and provenance should be validated.
- Golden images should be patched, hardened, scanned, and versioned.
- Cloud images generally work best with cloud-init and virtio drivers.

Useful checks:

```bash
kubectl get virtualmachineimages -A
kubectl describe virtualmachineimage <image-name> -n <namespace>
kubectl get pvc -A
kubectl -n longhorn-system get volumes.longhorn.io
```

### L3 troubleshooting

If import is stuck, inspect events, importer/data-volume pods, PVC status, DNS/proxy reachability, certificates, free capacity, and Longhorn health. A successful HTTP response from a workstation does not prove the Harvester cluster can reach the image URL.

---

## Q18. What is cloud-init, and how is it used with Harvester VMs?

### Detailed answer

Cloud-init initializes a guest on first boot. Harvester can supply user-data and network-data to configure:

- Hostname.
- Users and SSH keys.
- Packages.
- Files.
- Commands.
- Network settings.
- Registration or bootstrap actions.

Example user-data:

```yaml
#cloud-config
hostname: app01
users:
  - name: ops
    groups: [sudo]
    shell: /bin/bash
    ssh_authorized_keys:
      - ssh-ed25519 AAAA...example
package_update: true
packages:
  - qemu-guest-agent
runcmd:
  - systemctl enable --now qemu-guest-agent
```

### Security considerations

Do not place long-lived passwords, tokens, private keys, or sensitive registration secrets directly in reusable cloud-init templates. Use short-lived bootstrap credentials or an external secrets workflow. Remember that user-data may be retrievable through platform APIs by users with sufficient access.

### Common failure causes

- Image lacks cloud-init.
- Wrong datasource.
- YAML formatting error.
- Network is unavailable during first boot.
- Package repository unreachable.
- Cloud-init already marked complete in a captured image.

Inside the guest:

```bash
cloud-init status --long
journalctl -u cloud-init -u cloud-final
cat /var/log/cloud-init.log
cat /var/log/cloud-init-output.log
```

---

## Q19. Explain VM CPU and memory sizing in Harvester.

### Detailed answer

VM requests influence Kubernetes scheduling, while guest topology influences performance. A senior administrator considers:

- Requested vCPU and memory.
- Host allocatable resources, not only physical totals.
- CPU overcommit policy.
- NUMA topology and large VM placement.
- CPU pinning or dedicated CPU requirements where supported.
- Huge pages for selected latency-sensitive workloads.
- Memory overcommit and ballooning behavior.
- Reserved resources for KubeVirt, Longhorn, RKE2, and system pods.

### Corporate rule

Avoid treating VM density as a purely arithmetic ratio. A host with 64 cores and 512 GiB RAM cannot necessarily run eight 8-vCPU/64-GiB VMs safely once storage engines, replicas, migration headroom, kernel memory, and system services are included.

### Troubleshooting Pending VMs

```bash
kubectl get vmi -A
kubectl describe vmi <vm> -n <ns>
kubectl get nodes -o custom-columns=NAME:.metadata.name,CPU:.status.allocatable.cpu,MEM:.status.allocatable.memory
kubectl describe node <node>
```

Look for `FailedScheduling` events and identify the exact predicate: insufficient CPU, memory, affinity mismatch, volume topology, network constraint, taint, or device limitation.

---

## Q20. What is live migration, and what conditions can prevent it?

### Detailed answer

Live migration moves a running VM from one node to another with minimal interruption. KubeVirt copies memory state while the VM continues running, then performs a short switchover.

Live migration may be blocked by:

- Non-migratable disk access mode or volume configuration.
- Host-device passthrough.
- CPU model incompatibility.
- Insufficient target-node memory or CPU.
- Network attachment unavailable on the destination.
- Anti-affinity or node selector constraints.
- Unhealthy storage.
- Excessive dirty-memory rate.
- Migration network failure.
- KubeVirt component health issues.

Commands:

```bash
kubectl get virtualmachineinstancemigrations -A
kubectl get vmi <vm> -n <ns> -o yaml
kubectl describe virtualmachineinstancemigration <migration> -n <ns>
kubectl get pods -A -l kubevirt.io=virt-launcher -o wide
```

### L3 perspective

Live migration is not guaranteed to complete quickly. A write-intensive VM can dirty memory faster than it can be transferred. Migration bandwidth, MTU, CPU compatibility, and target capacity must be tested with production-like workloads.

---

## Q21. How do node maintenance and VM eviction work in Harvester?

### Detailed answer

Maintenance operations usually involve cordoning and draining a node. Harvester and KubeVirt determine whether VMs can be live-migrated, shut down, or block the drain based on policy and migratability.

Safe workflow:

1. Confirm cluster, storage, and network health.
2. Confirm enough target capacity.
3. Identify VMs on the node.
4. Validate each VM's migration capability.
5. Enter maintenance mode using the supported Harvester workflow.
6. Monitor migrations, volume attachment, and node drain.
7. Perform maintenance.
8. Return the node and verify Longhorn replica health before the next node.

Useful commands:

```bash
kubectl get vmi -A -o wide
kubectl get nodes
kubectl get virtualmachineinstancemigrations -A
kubectl -n longhorn-system get volumes.longhorn.io
kubectl get events -A --sort-by=.lastTimestamp | tail -100
```

### Dangerous practice

Do not force-delete `virt-launcher` pods or detach Longhorn volumes just to make a drain complete unless the supported recovery procedure explicitly requires it. This can create VM outage or data risk.

---

## Q22. How do VM templates improve enterprise operations?

### Detailed answer

VM templates standardize deployment. They can define:

- Base image.
- CPU and memory.
- Disk layout.
- Network attachments.
- Cloud-init.
- Labels and annotations.
- Scheduling policy.
- Boot order and firmware.

Benefits include consistency, faster provisioning, auditability, and fewer configuration errors.

### Governance model

Use separate template lifecycle stages:

```text
Build → security scan → functional test → performance test
→ approval → publish → deprecate → retire
```

Version templates instead of editing a widely used template in place. Maintain image SBOMs, patch dates, hardening standard, guest-agent version, and owner metadata. A corporate template must have a defined refresh cadence and retirement process.

---

## Q23. How do you troubleshoot a Harvester VM that is stuck in Starting?

### Detailed answer

Use a layered workflow.

### Step 1: Inspect the VM and VMI

```bash
kubectl get vm,vmi -n <namespace>
kubectl describe vm <vm> -n <namespace>
kubectl describe vmi <vm> -n <namespace>
```

Focus on events and conditions.

### Step 2: Inspect `virt-launcher`

```bash
kubectl get pods -n <namespace> -o wide | grep virt-launcher
kubectl describe pod <virt-launcher-pod> -n <namespace>
kubectl logs <virt-launcher-pod> -n <namespace> --all-containers
```

### Step 3: Check disks

```bash
kubectl get pvc -n <namespace>
kubectl describe pvc <pvc> -n <namespace>
kubectl -n longhorn-system get volumes.longhorn.io
```

### Step 4: Check networks

```bash
kubectl get network-attachment-definitions -A
kubectl describe network-attachment-definition <nad> -n <namespace>
kubectl get nodes --show-labels
```

### Step 5: Check node capability

```bash
kubectl describe node <node>
journalctl -u rke2-agent --since '30 min ago'
lsmod | grep kvm
```

Classify the issue as scheduling, storage, networking, image, admission, KubeVirt, or host virtualization. Avoid random restarts until evidence is collected.

---

## Q24. How do you handle a VM that is running but not reachable?

### Detailed answer

Separate guest failure from platform-network failure.

1. Open the VM console. If login works, inspect guest networking.
2. Confirm the VM NIC is attached to the intended network.
3. Check guest IP, route, DNS, firewall, and interface status.
4. Verify VLAN is permitted on the switch trunk.
5. Verify Harvester cluster network and node network configuration.
6. Confirm the VM is scheduled on a node where that network is enabled.
7. Check MTU and ARP/neighbor behavior.
8. Capture traffic at guest tap, host bridge, bond, and physical interface where required.

Commands:

```bash
kubectl get vmi <vm> -n <ns> -o wide
kubectl describe vmi <vm> -n <ns>
nmcli connection show
ip -br link
bridge link
bridge vlan show
ip neigh
```

Inside the guest:

```bash
ip -br addr
ip route
resolvectl status
ping -c 3 <gateway>
tracepath <destination>
ss -lntup
```

A reachable gateway but unreachable remote service suggests routing/firewall. No ARP resolution for the gateway suggests VLAN, bridge, bond, switch, or guest NIC configuration.

---

# 4. Harvester Networking

## Q25. Explain Harvester management, VLAN, and untagged VM networks.

### Detailed answer

Harvester provides multiple VM network types.

### Management network

- Built-in cluster network.
- Suitable mainly for in-cluster or management-style connectivity.
- Uses the cluster overlay.
- VM interface MTU may be lower than the host management MTU because of overlay encapsulation.
- VM IP behavior differs from conventional static external networks unless the guest or Kubernetes service configuration provides stability.

### VLAN network

- Connects VMs to an external layer-2 VLAN.
- Uses Multus and bridge CNI.
- Requires the VLAN to be allowed on the physical switch path.
- Appropriate for production server networks.

### Untagged network

- Connects VMs to an untagged external layer-2 network through the configured bridge/uplink.
- Useful where the switch port presents a native/access network.

### L3 risk

A network may exist in the Harvester UI but not be operational on every node. Scheduling a VM onto a node without the corresponding cluster-network configuration can fail or be prevented. Node selectors and network configuration must be consistent with intended placement.

---

## Q26. What are Multus and NetworkAttachmentDefinition objects?

### Detailed answer

Kubernetes normally gives a pod one primary network interface through the default CNI. Multus acts as a meta-plugin that can attach additional interfaces. KubeVirt uses these additional attachments for VM production networks.

A `NetworkAttachmentDefinition` (NAD) stores the CNI configuration for an additional network. Harvester creates and manages these resources through its higher-level network objects.

Commands:

```bash
kubectl get network-attachment-definitions -A
kubectl get network-attachment-definition <name> -n <namespace> -o yaml
kubectl get pod <virt-launcher-pod> -n <namespace> -o yaml
```

Typical bridge-CNI concepts include:

```json
{
  "type": "bridge",
  "bridge": "mgmt-br",
  "vlan": 100,
  "promiscMode": true
}
```

Do not manually edit generated NADs without understanding the Harvester controller's reconciliation behavior. Make supported changes through Harvester network resources or documented APIs.

---

## Q27. How does VLAN traffic flow from a Harvester VM to the physical network?

### Detailed answer

Simplified path:

```text
Guest NIC
  ↓
KubeVirt tap/interface in virt-launcher pod
  ↓
Multus + bridge CNI attachment
  ↓
Host Linux bridge
  ↓
Host bond/uplink
  ↓
Physical NIC
  ↓
Switch trunk
  ↓
VLAN gateway or peer
```

At each layer, validate:

- Interface state.
- VLAN tag.
- Bridge membership.
- Bond slave state.
- Physical link.
- Switch trunk allow list.
- MTU.
- MAC learning and port-security policy.

Commands:

```bash
ip -d link show
bridge link
bridge vlan show
nmcli device status
cat /proc/net/bonding/<bond-name>
ethtool <nic>
ip -s link show <nic>
```

A frequent enterprise issue is that a new VLAN was created in Harvester but not allowed on one or more switch trunks. The VM works on some nodes and fails after migration to others.

---

## Q28. Why is MTU consistency critical in Harvester?

### Detailed answer

MTU mismatch can cause packet loss, fragmentation, stalled TLS, poor migration performance, storage instability, or an application that works for small packets but fails for larger ones.

Check every layer:

- Guest NIC.
- Tap interface.
- CNI interface.
- Bridge.
- Bond.
- Physical NIC.
- Physical switch.
- Router/firewall.
- Storage network.
- Overlay encapsulation overhead.

Commands:

```bash
ip link show
nmcli -f GENERAL.DEVICE,GENERAL.MTU device show
ping -M do -s 1472 <peer>       # Example for IPv4 MTU 1500 path
tracepath <peer>
```

The payload size for `ping` must account for IP and ICMP headers. Use controlled testing and do not assume jumbo frames are enabled just because the host NIC shows MTU 9000.

---

## Q29. How would you troubleshoot intermittent network loss after VM live migration?

### Detailed answer

Likely areas include:

- Destination node lacks correct VLAN/network configuration.
- Switch MAC table does not update promptly.
- Port security limits MAC movement.
- Bond or LACP inconsistency on one node.
- Gratuitous ARP or neighbor update is blocked.
- MTU differs between source and destination paths.
- Network policy or firewall is node-dependent.
- Guest driver or interface state changes during migration.

Workflow:

1. Record source and destination nodes.
2. Compare `nmcli`, bridge, bond, VLAN, and MTU configuration.
3. Check switch logs and MAC-address-table movement.
4. Capture ARP and data traffic on the destination.
5. Confirm the VM MAC is learned on the expected port channel.
6. Test repeated controlled migrations.

```bash
tcpdump -eni <bridge-or-uplink> 'arp or icmp'
bridge fdb show | grep -i <vm-mac>
ip neigh show
```

The correct fix is consistency across nodes and switches, not disabling migration or forcing static neighbor entries unless explicitly designed.

---

## Q30. What is a Harvester storage network, and why use one?

### Detailed answer

A storage network separates Longhorn replication and storage traffic from general management and workload traffic. Benefits include:

- Reduced contention.
- Predictable latency.
- Easier QoS and capacity planning.
- Better fault isolation.
- Cleaner troubleshooting.

However, a storage network adds complexity:

- Additional NICs, bonds, VLANs, IP addresses, and switch configuration.
- More MTU and routing dependencies.
- Upgrade-time IP requirements.
- Risk of storage unavailability if the network is misconfigured.

### Validation

Measure packet loss, retransmissions, interface errors, queue drops, and throughput during steady state and replica rebuild. A storage network with a single oversubscribed switch is not true failure isolation.

---

## Q31. How do you expose a VM service when the VM uses the management network?

### Detailed answer

Because management-network VM addresses are not equivalent to conventional externally routed addresses, a Kubernetes Service can provide a stable service endpoint. Depending on requirements, use ClusterIP, NodePort, or a supported load-balancer implementation.

Before choosing this model, evaluate:

- Whether clients are inside or outside the cluster.
- Source IP preservation.
- High availability.
- Port range and firewall rules.
- Service ownership and RBAC.
- Whether a VLAN-backed VM NIC would be simpler.

For traditional enterprise server workloads that require stable routable addresses and existing firewall policies, a VLAN or untagged network is usually more natural than exposing every VM service through Kubernetes services.

---

## Q32. What network evidence should be collected before escalating a Harvester issue?

### Detailed answer

Collect:

- Harvester support bundle.
- Node and VM placement.
- VM MAC and IP addresses.
- Network object and NAD YAML.
- `nmcli` connection and device state.
- `ip -d link`, `bridge link`, and `bridge vlan show`.
- Bond state from `/proc/net/bonding`.
- NIC counters and driver/firmware from `ethtool`.
- Switch port-channel, VLAN, MAC-table, error, and spanning-tree information.
- Packet captures at logical checkpoints.
- Exact timestamps and migration/failure sequence.

Example:

```bash
kubectl get nodes -o wide
kubectl get vmi -A -o wide
kubectl get network-attachment-definitions -A -o yaml
nmcli connection show
ip -s link
ethtool -S <nic>
tcpdump -s 0 -w /tmp/harvester-net.pcap -i <interface>
```

Sanitize secrets and personally identifiable data before sharing captures or bundles externally.

---

# 5. Harvester Storage, Backup, and DR

## Q33. How does Longhorn store Harvester VM disks?

### Detailed answer

Longhorn provides distributed block volumes. A VM disk is commonly represented by a Kubernetes PVC backed by a Longhorn volume. Longhorn creates replicas on eligible disks/nodes according to the StorageClass and scheduling policy.

Simplified path:

```text
VM guest block device
  ↓
KubeVirt disk attachment
  ↓
PVC / Longhorn volume
  ↓
Longhorn engine
  ↓
Replicas on node data disks
```

A volume with three replicas does not mean three independent backups. Replication protects availability against certain node or disk failures, but accidental deletion, corruption, ransomware, operator error, or cluster-wide disaster can affect all replicas. External backups are still required.

---

## Q34. What is the difference between a Longhorn replica, snapshot, and backup?

### Detailed answer

### Replica

A live copy of volume data maintained on another eligible disk/node. It supports availability and rebuild after failure.

### Snapshot

A point-in-time state maintained within the storage system. It is convenient and fast but normally remains within the same cluster/storage failure domain.

### Backup

A copy sent to an external backup target, such as supported S3-compatible object storage or NFS, depending on the configured product workflow. It provides protection beyond the local cluster.

### Interview statement

```text
Replication is for availability.
Snapshots are for local point-in-time recovery.
Backups are for disaster recovery and independent retention.
```

A complete design also includes immutability or object lock where possible, separate credentials, restricted deletion permissions, monitoring, retention policy, and regular restore testing.

---

## Q35. How do StorageClasses influence Harvester VM storage?

### Detailed answer

StorageClasses define Longhorn provisioning behavior, including policies such as:

- Number of replicas.
- Disk or node selection.
- Data locality.
- Replica auto-balance settings.
- Filesystem or volume parameters where applicable.
- Reclaim policy and expansion behavior.

Use different classes for different service tiers:

```text
Gold: 3 replicas, fast SSD/NVMe, strict placement
Silver: 2 or 3 replicas, general-purpose SSD
Archive/dev: lower-cost policy with reduced SLA
```

### L3 warning

Reducing replicas to one may improve capacity efficiency but creates a single failure point for that volume. Increasing replicas consumes more raw capacity and rebuild bandwidth. Select the policy based on application criticality and failure model, not only free disk percentage.

Commands:

```bash
kubectl get storageclass
kubectl get storageclass <name> -o yaml
kubectl get pvc -A -o wide
kubectl -n longhorn-system get volumes.longhorn.io
```

---

## Q36. How do you diagnose a degraded Longhorn volume?

### Detailed answer

A degraded volume has fewer healthy replicas than desired. Investigate:

1. Which volume and VM are affected.
2. Desired versus healthy replica count.
3. Failed node or disk.
4. Available capacity on eligible disks.
5. Scheduling restrictions and tags.
6. Network connectivity between engine and replicas.
7. Disk latency, I/O errors, or filesystem errors.
8. Whether a rebuild is active or repeatedly failing.

Commands:

```bash
kubectl -n longhorn-system get volumes.longhorn.io
kubectl -n longhorn-system get replicas.longhorn.io
kubectl -n longhorn-system describe volume <volume-name>
kubectl -n longhorn-system get pods -o wide
kubectl get events -A --sort-by=.lastTimestamp | tail -100
dmesg -T | egrep -i 'error|timeout|reset|nvme|scsi|xfs|ext4'
```

### Production response

Do not immediately delete a failed replica. First verify that another healthy replica exists and that the volume is attached correctly. Preserve logs and determine whether the root cause is disk, node, network, capacity, or scheduling policy.

---

## Q37. Why can a Longhorn replica rebuild affect production performance?

### Detailed answer

A rebuild reads a large amount of data from a healthy replica and writes it to a new replica. It consumes:

- Source-disk read I/O.
- Destination-disk write I/O.
- Storage-network bandwidth.
- CPU and memory.
- Longhorn engine resources.

If multiple volumes rebuild simultaneously, latency can increase sharply. Controls include rebuild concurrency, rate planning, storage-network isolation, maintenance windows, and sufficient free capacity.

### L3 practice

Establish normal and rebuild baselines:

```bash
iostat -x 1
sar -n DEV 1
pidstat -d 1
kubectl top nodes
```

Monitor application latency as the primary service metric. A storage device can show moderate utilization while queue latency or network retransmissions still cause VM slowdown.

---

## Q38. How should Harvester VM backups be designed?

### Detailed answer

A production backup design defines:

- Which VMs and disks are protected.
- Recovery point objective (RPO).
- Recovery time objective (RTO).
- Backup frequency and retention.
- External backup target.
- Encryption in transit and at rest.
- Credential isolation.
- Immutability/object lock where available.
- Application consistency requirements.
- Restore testing schedule.
- Cross-cluster or site-recovery process.

### Crash-consistent versus application-consistent

A platform snapshot may be crash-consistent. Databases and transactional applications may require guest-agent coordination, filesystem freeze, database backup commands, or application-native backup to ensure recoverability.

### Corporate rule

A backup is not considered successful merely because a job status says Completed. Validate object existence, size, checksum where available, retention, and periodic restore into an isolated environment.

---

## Q39. How would you restore a failed Harvester VM?

### Detailed answer

The exact workflow depends on whether recovery uses a local snapshot, VM backup, application backup, or cross-cluster copy.

General process:

1. Identify the required recovery point.
2. Confirm backup health and application owner approval.
3. Avoid overwriting the original VM until validation.
4. Restore to a new VM/name or isolated namespace when possible.
5. Attach to an isolated network first.
6. Boot and verify filesystem/application integrity.
7. Validate identity, hostname, IP, certificates, and external integrations.
8. Obtain application-owner sign-off.
9. Perform controlled cutover.

### L3 concerns

- Duplicate IP or hostname.
- Database split brain.
- Old credentials or certificates.
- Restored node rejoining an existing cluster unexpectedly.
- Message replay.
- Inconsistent multi-disk recovery points.
- Backup created during active writes without guest coordination.

---

## Q40. How do you protect Harvester from a cluster-wide storage disaster?

### Detailed answer

Use layered protection:

- Longhorn replicas distributed across failure domains.
- External VM backups outside the Harvester cluster.
- Object storage in another fault domain or site.
- Separate backup credentials with least privilege.
- Immutability/object lock and retention controls.
- Offline or logically isolated copy for critical data.
- Configuration-as-code for network, template, and policy reconstruction.
- Documented bare-metal reinstall and cluster rebuild process.
- Application-native backups for databases and stateful services.
- Regular disaster-recovery exercises.

A replica on another node in the same rack is not site DR. An S3-compatible endpoint running as VMs on the same Harvester cluster is also not independent DR unless its data is externally replicated and recoverable without that cluster.

---

# 6. Rancher Multi-Cluster Administration

## Q41. How does Rancher import and manage an existing Kubernetes cluster?

### Detailed answer

Rancher generates registration resources that deploy agents into the target cluster. These agents establish communication with Rancher and allow Rancher to observe and manage cluster resources.

Important agents include components in namespaces such as `cattle-system`. Connectivity generally requires downstream nodes/agents to reach the Rancher URL and trust its certificate chain.

Troubleshooting commands:

```bash
kubectl -n cattle-system get pods -o wide
kubectl -n cattle-system logs deploy/cattle-cluster-agent --since=20m
kubectl -n cattle-system describe pod -l app=cattle-cluster-agent
curl -vk https://rancher.example.com/ping
```

### Common causes of Disconnected status

- DNS failure.
- Firewall/proxy block.
- Incorrect `NO_PROXY`.
- Private CA not trusted.
- Expired or mismatched certificate.
- WebSocket/idle timeout issue on load balancer.
- Agent crash or image-pull failure.
- Cluster time skew.

---

## Q42. Explain Rancher global roles, cluster roles, and project roles.

### Detailed answer

Rancher adds an authorization model above native Kubernetes RBAC.

- **Global roles** control access to Rancher-wide capabilities.
- **Cluster roles/memberships** control access within a managed cluster.
- **Project roles/memberships** control access within a Rancher project and its namespaces.
- **Native Kubernetes RBAC** still controls API permissions through Roles, ClusterRoles, RoleBindings, and ClusterRoleBindings.

### L3 governance principle

Do not grant global administrator to solve routine access issues. Build persona-based roles:

```text
Platform administrator
Cluster operator
Project administrator
Application deployer
Read-only auditor
Security reviewer
Backup operator
```

Test effective access using impersonation and `kubectl auth can-i`. Review both Rancher role templates and native bindings because permissions may be inherited from several sources.

```bash
kubectl auth can-i --list --as=<user> -n <namespace>
kubectl get rolebindings,clusterrolebindings -A
```

---

## Q43. What is a Rancher project, and how is it different from a namespace?

### Detailed answer

A Kubernetes namespace is a native isolation and naming boundary. A Rancher project groups one or more namespaces so administrators can apply access control and resource policies at a higher level.

Projects are useful for:

- Team-level access.
- Quotas across multiple namespaces.
- Consistent policy application.
- Simplified delegation.

A project is not a hard network or security boundary by itself. NetworkPolicy, Pod Security controls, quotas, RBAC, secret management, admission policy, and cluster design must still be implemented.

### Corporate model

```text
Cluster
  ├─ Project: Payments
  │   ├─ payments-dev
  │   ├─ payments-test
  │   └─ payments-prod
  └─ Project: Analytics
      ├─ analytics-dev
      └─ analytics-prod
```

For strong production separation, many organizations use separate clusters rather than relying only on projects and namespaces.

---

## Q44. What is Fleet, and how does Rancher use it?

### Detailed answer

Fleet is Rancher’s GitOps/continuous-delivery engine. It deploys bundles from Git repositories to one or more clusters based on selectors and targets.

Fleet concepts include:

- Git repositories.
- Bundles.
- Cluster groups.
- Targets and selectors.
- Helm or raw manifest deployment.
- Drift detection and reconciliation.

### Production use cases

- Baseline namespaces and policies.
- Monitoring agents.
- Ingress controllers.
- Security configuration.
- Platform add-ons.
- Application deployment across edge clusters.

### L3 controls

- Protect Git branches.
- Require pull-request review.
- Pin chart and image versions.
- Separate environments.
- Store no plaintext secrets.
- Use progressive rollout/canary groups.
- Define rollback through Git.
- Monitor bundle readiness and drift.

Troubleshooting:

```bash
kubectl get gitrepos -A
kubectl get bundles -A
kubectl get bundledeployments -A
kubectl -n cattle-fleet-system get pods
kubectl -n cattle-fleet-system logs deploy/fleet-controller --since=20m
```

---

## Q45. How do you upgrade Rancher safely?

### Detailed answer

A safe Rancher upgrade is a controlled change, not only a Helm command.

### Process

1. Read release notes and known issues.
2. Check Rancher/Kubernetes/cert-manager compatibility.
3. Upgrade to the latest patch of the current minor release before crossing minor versions when required.
4. Back up Rancher using the supported backup operator.
5. Back up the management Kubernetes cluster according to its distribution.
6. Export current Helm values and chart version.
7. Validate DNS, LB, certificates, and free resources.
8. Rehearse in non-production.
9. Upgrade during an approved window.
10. Monitor Rancher, agents, Fleet, authentication, and downstream connectivity.
11. Keep rollback instructions and backup immediately available.

Commands:

```bash
helm -n cattle-system list
helm -n cattle-system get values rancher -o yaml > rancher-values-before.yaml
kubectl -n cattle-system get pods -o wide
helm repo update
helm upgrade rancher <repo>/rancher -n cattle-system \
  --version <approved-version> \
  -f rancher-values.yaml
```

Never assume Helm rollback alone restores Rancher application state. Follow the product-supported rollback/restore procedure.

---

## Q46. What exactly does the Rancher backup operator protect?

### Detailed answer

The Rancher backup/restore operator protects Rancher application resources in the local cluster. It does not automatically replace:

- A full management-cluster disaster-recovery plan.
- Downstream-cluster etcd backups.
- Application PVC backups.
- External identity-provider configuration.
- External DNS/load-balancer configuration.
- All files stored outside Kubernetes resources.
- Release-specific encryption configuration files unless explicitly handled.

### Corporate backup set

Maintain:

1. Rancher application backup.
2. Management-cluster etcd/distribution backup.
3. Helm values and infrastructure-as-code.
4. Certificates and CA recovery procedures.
5. DNS/LB configuration.
6. Identity-provider configuration.
7. Private registry and chart repositories.
8. Downstream-cluster backups.

Restore tests must use compatible Rancher and Kubernetes versions according to the official procedure.

---

## Q47. How do you troubleshoot Rancher UI slowness?

### Detailed answer

Classify the bottleneck:

- Browser/UI plugin issue.
- Rancher server CPU or memory pressure.
- Management-cluster API latency.
- etcd latency.
- DNS or load-balancer latency.
- Large number of clusters/resources.
- Downstream agent instability.
- WebSocket/proxy timeout.
- Monitoring/logging contention.

Commands:

```bash
kubectl -n cattle-system top pods
kubectl top nodes
kubectl -n cattle-system get pods -o wide
kubectl -n cattle-system logs deploy/rancher --since=20m | tail -500
kubectl get --raw='/readyz?verbose'
curl -sS -o /dev/null -w '%{time_namelookup} %{time_connect} %{time_starttransfer} %{time_total}\n' https://rancher.example.com/ping
```

Use browser developer tools to distinguish slow static assets, API calls, authentication redirects, or WebSocket failures. Correlate timestamps with Rancher and ingress logs.

---

## Q48. How do you troubleshoot a downstream cluster shown as Unavailable or Disconnected?

### Detailed answer

Check in this order:

1. Is the downstream Kubernetes API healthy independently of Rancher?
2. Are `cattle-cluster-agent` and related pods running?
3. Can the agent resolve Rancher DNS?
4. Can it establish TLS to Rancher on TCP 443?
5. Does it trust the CA?
6. Is a proxy intercepting or blocking WebSockets?
7. Are Rancher server pods and ingress healthy?
8. Did a certificate, URL, load balancer, or firewall change occur?

```bash
kubectl get nodes
kubectl -n cattle-system get pods
kubectl -n cattle-system logs deploy/cattle-cluster-agent --since=30m
kubectl -n cattle-system get events --sort-by=.lastTimestamp
curl -vk https://rancher.example.com/ping
```

Do not re-import or delete the cluster as the first action. This can complicate recovery and destroy evidence. Resolve connectivity or certificate issues first.

---

## Q49. How does Rancher manage Kubernetes version upgrades for downstream clusters?

### Detailed answer

Rancher exposes supported Kubernetes versions based on the distribution, Rancher release, and support matrix. Upgrade orchestration differs for RKE2, K3s, hosted providers, imported clusters, and older RKE1 clusters.

A production workflow includes:

- Check version skew and deprecations.
- Review removed APIs.
- Validate CNI, CSI, ingress, admission, and monitoring compatibility.
- Take etcd snapshots/backups.
- Confirm node capacity for rolling upgrade.
- Upgrade control-plane/etcd nodes and workers in supported order.
- Monitor system pods and application SLOs.
- Test rollback/restore limitations.

### Critical point

Rancher’s availability does not guarantee the downstream upgrade is safe. The cluster’s own control plane, etcd, workloads, PodDisruptionBudgets, storage, and application compatibility must be assessed.

---

## Q50. What is the difference between Rancher provisioning, importing, and registering clusters?

### Detailed answer

- **Provisioning** means Rancher participates in creating the infrastructure or Kubernetes cluster using a supported driver or provisioning method.
- **Importing/registering** means the Kubernetes cluster already exists and Rancher agents are installed so Rancher can manage it.
- **Hosted clusters** may be registered with provider-specific integrations while the cloud provider controls some lifecycle operations.

### Operational difference

For a Rancher-provisioned cluster, Rancher may hold machine plans, bootstrap configuration, node-driver configuration, and upgrade controls. For an imported cluster, Rancher normally does not own the underlying node lifecycle unless additional tooling is integrated.

Before troubleshooting, establish who owns:

```text
Infrastructure → OS → Kubernetes → CNI/CSI → Rancher agent → Applications
```

This avoids attempting a Rancher-side fix for an externally managed infrastructure failure.

---

# 7. Security, Governance, and Enterprise Operations

## Q51. How do you harden Rancher Manager?

### Detailed answer

Key controls include:

- Dedicated management cluster.
- Supported patched versions.
- Trusted TLS certificate and secure cipher policy at ingress/LB.
- External identity provider with MFA where available.
- Least-privilege global, cluster, and project roles.
- Restricted local administrator accounts.
- API audit logging.
- Network segmentation and firewall rules.
- Private registry and image governance.
- Secret-management integration.
- Backup encryption and restricted restore permissions.
- Monitoring and alerting for authentication, agent, certificate, and backup failures.
- Regular access review.
- Controlled feature flags and UI extensions.
- CIS-aligned Kubernetes configuration where required.

### Senior-level concern

Rancher is a high-value management plane. Compromise can provide access to many downstream clusters. Protect it like an identity or privileged-access system, not like an ordinary application dashboard.

---

## Q52. How do you harden Harvester?

### Detailed answer

Controls include:

- Isolated management and BMC networks.
- Restricted API/UI access.
- External identity integration through Rancher where applicable.
- Least-privilege RBAC.
- Secure boot/UEFI strategy where supported.
- Firmware and platform patching.
- Strong node and cluster credentials.
- Trusted VM images and image scanning process.
- Network segmentation using VLANs and firewall controls.
- Storage encryption and backup encryption where supported.
- Restricted access to embedded Longhorn and system namespaces.
- Audit logging and centralized monitoring.
- Controlled support-bundle handling because bundles can contain sensitive configuration.
- Physical security for nodes and data disks.

Do not treat VM isolation as sufficient if a user has broad Kubernetes or Harvester cluster-level privileges. Cluster administrators can often access highly sensitive platform resources.

---

## Q53. What identity-provider design would you recommend for Rancher?

### Detailed answer

Use a corporate identity provider such as LDAP/Active Directory, SAML, or OIDC based on organizational standards and supported integrations.

Design principles:

- Use groups rather than individual assignments.
- Map groups to least-privilege Rancher roles.
- Require MFA at the identity provider.
- Keep a tightly controlled break-glass local administrator.
- Test IdP certificate and metadata rotation.
- Document behavior when the IdP is unavailable.
- Review group nesting and stale membership.
- Use short session duration for privileged roles where appropriate.
- Log authentication and authorization events.

### Lockout prevention

Before changing authentication configuration, verify a local break-glass account, record the rollback procedure, and test with a non-privileged user. Do not disable the only working administrative authentication path until the new provider is confirmed.

---

## Q54. How would you implement separation of duties?

### Detailed answer

Define responsibilities such as:

| Role | Typical access |
|---|---|
| Rancher platform admin | Management plane and cluster registration |
| Harvester infrastructure admin | Hosts, networks, storage, VM platform |
| Kubernetes cluster admin | Downstream cluster lifecycle |
| Project admin | Namespaces and team workloads |
| VM operator | Start/stop/console within assigned namespace |
| Backup operator | Backup execution without general admin |
| Security auditor | Read-only configuration and logs |
| Network admin | Physical switching/VLAN/firewall changes |

No single application user should automatically receive host, Longhorn, or cluster-admin access. High-risk actions such as platform upgrade, restore, host removal, and backup deletion should require approved change records and peer review.

---

## Q55. What should be monitored in Rancher and Harvester?

### Detailed answer

### Rancher

- Rancher pod availability and restarts.
- CPU/memory saturation.
- Ingress and LB latency/errors.
- TLS expiration.
- Authentication failures.
- Agent connectivity.
- Fleet bundle readiness.
- Backup job success.
- Management-cluster API and etcd health.

### Harvester

- Node readiness and pressure conditions.
- Control-plane health.
- VM state and migration failures.
- Longhorn volume health and replica count.
- Disk capacity, latency, and errors.
- Storage-network packet loss/retransmissions.
- VM network drops and bond state.
- Upgrade status.
- Backup success and target reachability.
- System partition free space.

### Alert quality

Alerts should be tied to action and service impact. For example, “Longhorn volume degraded for 15 minutes” is more actionable than a raw pod restart alert. Include runbook links, ownership, severity, and escalation thresholds.

---

## Q56. How do you perform capacity planning for Harvester?

### Detailed answer

Model four separate resources:

1. **Compute:** requested and actual CPU usage, overcommit, peak utilization.
2. **Memory:** reservations, working set, host/system reserve, failover capacity.
3. **Storage capacity:** logical provisioned, actual used, replica multiplier, snapshots, backup staging, rebuild headroom.
4. **Storage/network performance:** IOPS, throughput, latency, rebuild and migration traffic.

Raw-capacity example:

```text
100 TiB physical raw
÷ 3 replicas ≈ 33.3 TiB theoretical usable
− system reserve
− rebuild headroom
− snapshot growth
− operational safety margin
= significantly less safe allocatable capacity
```

Do not promise capacity using only the raw-to-replica division. Workload distribution, disk imbalance, failed-node rebuild, and StorageClass restrictions can make some capacity unusable.

---

## Q57. What changes require formal change management?

### Detailed answer

Examples:

- Rancher or Harvester upgrades.
- Kubernetes version changes.
- CNI/CSI changes.
- StorageClass changes.
- Replica-count changes.
- Node addition/removal.
- NIC bonding or VLAN changes.
- Management IP, DNS, LB, proxy, or certificate changes.
- Authentication-provider changes.
- Backup target or retention changes.
- Cluster-wide RBAC or admission policy changes.
- Enabling experimental features or add-ons.

A change record should include business justification, impact, dependencies, compatibility evidence, pre-checks, backup, implementation, monitoring, rollback, owner, and validation criteria.

---

# 8. Rancher–Harvester Integration

## Q58. How does Rancher integrate with Harvester?

### Detailed answer

Rancher can import Harvester as a virtualization cluster and provide access to Harvester management through Rancher. Rancher can also use the Harvester node driver to provision VMs that become nodes of downstream RKE2 or K3s clusters.

Integration use cases:

- Centralized access to multiple Harvester clusters.
- Provision Kubernetes clusters on Harvester VMs.
- Use Harvester cloud-provider and CSI integration for guest clusters.
- Standardize VM images and node templates.
- Apply Rancher RBAC to Harvester access.

Connectivity requirement: Harvester nodes and provisioned cluster nodes must be able to reach Rancher’s load-balanced endpoint, generally on TCP 443, and must trust its certificate.

---

## Q59. Explain the Harvester node driver workflow in Rancher.

### Detailed answer

High-level workflow:

1. Import or connect Harvester to Rancher.
2. Configure cloud credentials or access context.
3. Create a machine/node template selecting image, CPU, memory, disk, network, SSH user, and cloud-init.
4. Create an RKE2/K3s cluster in Rancher using the Harvester infrastructure provider/node driver.
5. Rancher requests VMs from Harvester.
6. VMs boot, run bootstrap configuration, and register as Kubernetes nodes.
7. Rancher reconciles the cluster lifecycle.

### Common failure points

- Wrong image or guest OS requirements.
- Missing `qemu-guest-agent` or cloud-init.
- Incorrect network or DHCP.
- Rancher URL unreachable.
- Private CA not trusted in guest.
- Node template references deleted Harvester resources.
- Insufficient Harvester compute/storage.
- Bootstrap token expiration.
- VM DNS or NTP problem.

---

## Q60. What are the Harvester cloud provider and CSI driver used for?

### Detailed answer

For Kubernetes clusters running as VMs on Harvester:

- The **cloud controller manager (CCM)** integrates Kubernetes node and load-balancer behavior with Harvester.
- The **CSI driver** allows guest Kubernetes clusters to dynamically provision persistent storage backed by Harvester/Longhorn.

This creates a layered storage path:

```text
Application pod PVC in guest Kubernetes
  ↓
Harvester CSI driver in guest cluster
  ↓
Harvester volume / Longhorn
  ↓
Physical disks
```

### L3 compatibility requirement

Use the documented support matrix for the exact Harvester, Rancher, RKE2, CCM, and CSI versions. A platform upgrade can affect guest storage or load-balancer behavior if integration components are not compatible.

---

## Q61. What is the data path when a Kubernetes workload runs on a Harvester VM?

### Detailed answer

Compute path:

```text
Application container
  ↓
Guest Kubernetes pod
  ↓
Guest node OS
  ↓
Harvester VM / KubeVirt
  ↓
QEMU/KVM
  ↓
Physical CPU and memory
```

Storage path with Harvester CSI:

```text
Guest pod filesystem
  ↓
Guest PVC/CSI
  ↓
Virtual disk attachment
  ↓
Harvester Longhorn volume
  ↓
Longhorn replicas
  ↓
Physical disks and storage network
```

Network path may include guest CNI overlay, VM virtual NIC, Harvester bridge/VLAN, host bond, and physical switch.

### Performance implication

There are multiple scheduling, storage, and network layers. Troubleshooting application latency must correlate guest metrics with Harvester VM, Longhorn, host, and physical-network metrics. Guest-level `iostat` alone cannot identify a Longhorn rebuild or host NIC drop.

---

## Q62. How do you design failure domains for Rancher-provisioned Kubernetes on Harvester?

### Detailed answer

Place guest control-plane nodes across different Harvester hosts and, where possible, racks or power domains. Use anti-affinity and machine pools so one host failure does not remove Kubernetes quorum.

Example design:

```text
Guest control-plane VM 1 → Harvester node A
Guest control-plane VM 2 → Harvester node B
Guest control-plane VM 3 → Harvester node C
Worker pools           → spread across A/B/C/D...
```

Also ensure Longhorn replicas are distributed appropriately. Compute anti-affinity without storage failure-domain awareness can still leave multiple guest control-plane disks exposed to the same disk or rack failure.

---

## Q63. Why can nested platform capacity become misleading?

### Detailed answer

When Kubernetes runs inside Harvester VMs, there are two resource-accounting layers:

- Harvester schedules the VMs.
- Guest Kubernetes schedules pods inside those VMs.

The guest cluster may report free allocatable CPU or memory even when the Harvester host is overcommitted. Conversely, Harvester may show allocated VM memory that is not currently used by guest applications.

Corporate capacity management should capture:

- Physical host utilization.
- VM requests and limits.
- Guest node allocatable resources.
- Pod requests and limits.
- Storage replica overhead.
- Migration and upgrade headroom.

Avoid aggressive overcommit at both layers simultaneously without performance testing and clear admission controls.

---

## Q64. How do you troubleshoot Rancher failing to provision a cluster on Harvester?

### Detailed answer

Follow the workflow end to end.

1. Inspect Rancher provisioning and machine objects.
2. Check Rancher provisioning logs and events.
3. Confirm Harvester API access and credentials.
4. Verify node template references.
5. Check whether VMs were created.
6. If created, inspect VM boot, cloud-init, networking, and Rancher reachability.
7. Confirm bootstrap and registration agents.
8. Check guest Kubernetes service logs.

Rancher-side checks:

```bash
kubectl get clusters.provisioning.cattle.io -A
kubectl get machines.cluster.x-k8s.io -A
kubectl get plans -A
kubectl -n cattle-system logs deploy/rancher --since=30m
```

Harvester-side checks:

```bash
kubectl get vm,vmi -A
kubectl get pods -A -l kubevirt.io=virt-launcher -o wide
kubectl get pvc -A
kubectl get events -A --sort-by=.lastTimestamp | tail -100
```

Inside the failed VM, inspect cloud-init, DNS, routes, NTP, and the RKE2/K3s service.

---

# 9. Troubleshooting and Corporate Scenarios

## Q65. A Harvester node becomes NotReady. What is your L3 response?

### Detailed answer

### Immediate goals

- Determine workload and storage impact.
- Preserve evidence.
- Prevent unsafe automated actions.
- Restore quorum and service safely.

### Workflow

1. Confirm whether the node is reachable through BMC, console, or SSH.
2. Check cluster node conditions and events.
3. Identify VMs and Longhorn replicas on the node.
4. Check management network, time, disk space, kernel, and RKE2 service.
5. Determine whether the node is failed, partitioned, overloaded, or rebooting.
6. Avoid deleting the node until storage and recovery implications are understood.

```bash
kubectl describe node <node>
kubectl get vmi -A -o wide | grep <node>
kubectl -n longhorn-system get replicas.longhorn.io -o wide | grep <node>
journalctl -u rke2-server --since '1 hour ago'
journalctl -u rke2-agent --since '1 hour ago'
df -h
dmesg -T | tail -200
```

If the node is permanently lost, follow supported node-removal and Longhorn replica-recovery procedures. Confirm healthy data copies before cleaning up failed replicas.

---

## Q66. The Harvester cluster has high storage latency. How do you isolate the cause?

### Detailed answer

Measure each layer.

### Guest

```bash
iostat -x 1
pidstat -d 1
```

### Longhorn

- Volume state.
- Replica rebuilds.
- Engine/replica CPU.
- Replica placement.
- Snapshot chain and recurring jobs.

### Host disk

```bash
iostat -x 1
sar -d 1
dmesg -T | egrep -i 'timeout|reset|error|nvme|scsi'
```

### Network

```bash
sar -n DEV,TCP,ETCP 1
ip -s link
ethtool -S <storage-nic>
```

### Correlation questions

- Did latency begin with a failed disk or replica rebuild?
- Is one node or disk slower?
- Are snapshots/backups running?
- Is storage traffic sharing a congested management link?
- Is free capacity low?
- Are multiple VM workloads aligned on the same replicas?
- Is the physical SSD throttling or worn?

Do not change Longhorn concurrency or disk scheduler parameters before establishing a baseline and rollback plan.

---

## Q67. Rancher is down, but downstream workloads are running. What do you do?

### Detailed answer

Treat this as a management-plane incident.

1. Confirm downstream applications remain healthy through direct cluster access.
2. Check Rancher DNS and load balancer.
3. Check management-cluster API and nodes.
4. Inspect Rancher pods, ingress, certificates, and events.
5. Determine whether the issue followed an upgrade, certificate rotation, or infrastructure change.
6. Protect backup and restore options.
7. Communicate that central management is affected while workloads remain operational.

```bash
kubectl -n cattle-system get pods -o wide
kubectl -n cattle-system describe deploy rancher
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl get ingress -A
kubectl get certificates -A
curl -vk https://rancher.example.com/ping
```

Avoid restarting all management nodes simultaneously. Maintain control-plane quorum and make one reversible change at a time.

---

## Q68. A Rancher upgrade failed. How do you decide between fix-forward and rollback?

### Detailed answer

Choose based on:

- Current management-plane availability.
- Data/schema migration state.
- Official known issue and supported procedure.
- Backup validity.
- Compatibility of previous and current versions.
- Time required to diagnose versus RTO.
- Risk to downstream management.

### Fix-forward is suitable when

- Failure is clearly external, such as image pull, certificate, or resource shortage.
- No incompatible data migration occurred.
- Official guidance supports correction and retry.

### Rollback/restore is suitable when

- Release has a confirmed blocking defect.
- Rancher remains unusable.
- A tested compatible backup exists.
- Official rollback steps are clear.

Never rely only on `helm rollback` for stateful application recovery. Use the documented Rancher backup/restore or rollback workflow.

---

## Q69. A Harvester upgrade is stuck in node pre-drain. What do you check?

### Detailed answer

Check:

- VMs on the node and their migratability.
- Active migrations.
- Longhorn volume health.
- PodDisruptionBudgets or system workloads blocking drain.
- Target-node capacity.
- Network attachments available on target nodes.
- Upgrade jobs and logs.

```bash
kubectl get vmi -A -o wide
kubectl get virtualmachineinstancemigrations -A
kubectl -n longhorn-system get volumes.longhorn.io
kubectl get pdb -A
kubectl -n harvester-system get jobs -l harvesterhci.io/upgradeComponent=node
kubectl -n harvester-system logs job/<pre-drain-job>
```

Generate a support bundle before destructive restart attempts. The bundle and upgrade logs together provide the current resource state and execution history.

---

## Q70. A VM cannot start because its volume is attached to another node. How do you respond?

### Detailed answer

This can occur after an unclean node failure, stalled detach, or control-plane inconsistency.

1. Confirm whether the original VM/QEMU process is truly stopped.
2. Confirm the old node is fenced or powered off to prevent dual attachment.
3. Inspect VMI, pod, PVC, VolumeAttachment, and Longhorn volume state.
4. Determine whether the attachment is stale or active.
5. Use the supported Longhorn/Harvester detach recovery procedure only after fencing.

```bash
kubectl get vmi -A -o wide
kubectl get pods -A -o wide | grep virt-launcher
kubectl get volumeattachments.storage.k8s.io
kubectl get pvc -A
kubectl -n longhorn-system get volumes.longhorn.io
```

### Safety principle

Never force-detach a writable volume while the old node may still be running the VM. This can cause two writers and filesystem corruption. Fencing is mandatory before forced recovery.

---

## Q71. One Harvester node has repeated disk errors. How do you evacuate it safely?

### Detailed answer

1. Identify affected disks, Longhorn replicas, and VMs.
2. Confirm other replicas are healthy.
3. Stop scheduling new replicas/workloads to the disk or node using supported controls.
4. Allow replicas to rebuild elsewhere.
5. Verify all protected volumes are healthy.
6. Live-migrate or shut down VMs according to policy.
7. Enter maintenance mode.
8. Replace hardware.
9. Validate firmware, SMART/NVMe health, and burn-in.
10. Re-enable scheduling gradually.

Commands:

```bash
smartctl -a /dev/<disk>
nvme smart-log /dev/<nvme-device>
dmesg -T | egrep -i 'I/O error|media|nvme|scsi|reset|timeout'
kubectl -n longhorn-system get replicas.longhorn.io -o wide
kubectl -n longhorn-system get volumes.longhorn.io
```

Do not simply remove a Longhorn disk containing the last healthy replica of a volume.

---

## Q72. A VM works on one Harvester node but fails on another. What is your hypothesis list?

### Detailed answer

Compare node-specific differences:

- VLAN allowed on switch trunk.
- Bond/LACP state.
- MTU.
- Network configuration or labels.
- CPU model/features.
- KVM modules.
- Disk or Longhorn path.
- Node taints and labels.
- Time synchronization.
- Firewall or routing.
- NIC driver/firmware.
- Resource pressure.

Useful comparison:

```bash
kubectl get nodes --show-labels
kubectl describe node <working-node>
kubectl describe node <failing-node>
nmcli connection show
bridge vlan show
cat /proc/net/bonding/<bond>
lscpu
lsmod | grep kvm
ip -s link
```

The fact that a VM works on one node proves the VM definition and guest image are probably valid; focus on node-local compute, network, storage, and configuration differences.

---

## Q73. Harvester reports low disk space during upgrade. What should be done?

### Detailed answer

Do not bypass free-space safety checks without understanding the risk.

1. Identify which partition or Longhorn disk is low.
2. Check images, snapshots, backups, logs, and unused volumes.
3. Remove only verified unused resources through supported workflows.
4. Confirm garbage collection and replica health.
5. Expand or add storage if needed.
6. Re-run official pre-checks.

```bash
df -h
du -xhd1 /var 2>/dev/null | sort -h
kubectl get virtualmachineimages -A
kubectl get pvc -A
kubectl -n longhorn-system get snapshots.longhorn.io
kubectl -n longhorn-system get volumes.longhorn.io
```

Skipping a threshold may allow the upgrade to start but fail later when images, temporary volumes, old/new system components, or logs require additional capacity.

---

## Q74. Rancher agents disconnect only through a corporate proxy. How do you troubleshoot?

### Detailed answer

Verify:

- Proxy URL and authentication.
- `HTTP_PROXY`, `HTTPS_PROXY`, and especially `NO_PROXY`.
- Rancher FQDN and internal cluster CIDRs excluded where required.
- WebSocket support through proxy.
- TLS inspection and CA trust.
- Idle timeout.
- DNS resolution path.

From the downstream cluster:

```bash
kubectl -n cattle-system exec deploy/cattle-cluster-agent -- env | grep -i proxy
kubectl -n cattle-system logs deploy/cattle-cluster-agent --since=30m
curl -vk https://rancher.example.com/ping
openssl s_client -connect rancher.example.com:443 -servername rancher.example.com
```

A proxy can permit ordinary HTTPS GET requests while breaking WebSocket upgrades or long-lived connections. Validate the actual agent path, not only browser access.

---

## Q75. Authentication works for local users but fails for LDAP/OIDC users. What do you inspect?

### Detailed answer

Check:

- IdP endpoint reachability from Rancher pods.
- DNS and proxy.
- CA trust.
- Client ID/secret or bind account.
- Redirect URI.
- Token claims and group attributes.
- Clock skew.
- Group search base/filter.
- User disabled or group nesting behavior.
- Rancher logs and IdP audit logs.

```bash
kubectl -n cattle-system logs deploy/rancher --since=30m | egrep -i 'auth|ldap|oidc|saml|error'
kubectl -n cattle-system exec deploy/rancher -- getent hosts <idp-host>
```

Preserve the local break-glass account while testing. Avoid repeated production edits without recording the previous configuration.

---

## Q76. A support bundle cannot be generated because a node is inaccessible. What do you do?

### Detailed answer

Generate the normal Harvester support bundle if possible. If a node is inaccessible or NotReady and centralized collection is incomplete, use the release-documented manual collection process on reachable nodes and gather the failed node’s console, journal, and hardware logs separately.

Collect:

- Support bundle.
- RKE2 journals.
- Kernel logs.
- Network state.
- Disk health.
- BMC event log.
- Exact failure timeline.
- Upgrade logs if related to an upgrade.

Do not reboot the node before preserving volatile logs unless service restoration requires immediate action and the incident commander approves it.

---

## Q77. How do you perform root-cause analysis for an intermittent Harvester outage?

### Detailed answer

Build a timeline across all layers:

```text
Application alert
→ guest OS symptoms
→ VM/KubeVirt events
→ Longhorn volume or network events
→ Harvester node state
→ switch/storage/hardware telemetry
→ change records
```

Evidence sources:

- Monitoring metrics.
- Kubernetes events.
- KubeVirt VM/VMI conditions.
- Longhorn events and replica state.
- `journalctl` and kernel logs.
- Network device logs.
- BMC/SEL logs.
- Rancher/Fleet activity.
- Backup or upgrade schedules.
- Configuration changes.

The RCA must identify the technical root cause, contributing factors, why monitoring did not prevent or detect it earlier, and corrective/preventive actions with owners and due dates.

---

## Q78. What are common anti-patterns in Harvester and Rancher operations?

### Detailed answer

- Running Rancher and user workloads on the same small cluster.
- Treating Longhorn replicas as backups.
- Using a backup target inside the same Harvester failure domain.
- Operating near maximum CPU, RAM, or disk utilization.
- Using inconsistent VLAN/MTU/bond configuration across nodes.
- Force-detaching volumes before fencing failed nodes.
- Skipping release notes and support matrices.
- Upgrading without tested backups.
- Granting cluster-admin/global-admin broadly.
- Storing secrets in Git or cloud-init.
- Manually editing controller-managed resources.
- Deleting disconnected clusters or nodes before evidence collection.
- Performing simultaneous maintenance on multiple quorum members.
- Relying only on UI status without command-line and infrastructure evidence.

---

## Q79. Design an enterprise DR strategy for Rancher plus Harvester.

### Detailed answer

### Rancher layer

- Rancher backup operator to external storage.
- Management-cluster etcd/distribution snapshots.
- Helm values and chart versions in Git.
- DNS, LB, CA, and identity configuration documented and backed up.
- Rebuild procedure for a clean compatible management cluster.

### Harvester layer

- VM backups to external target in another failure domain.
- Application-native backups for databases.
- Golden images and templates versioned externally.
- Network/VLAN/IP plan documented.
- Bare-metal installation automation and tokens secured.
- Replacement hardware and firmware baseline.

### DR exercise

1. Rebuild Rancher management cluster.
2. Restore Rancher at a compatible version.
3. Reconnect/import Harvester.
4. Restore representative VMs to an isolated network.
5. Validate application data and dependencies.
6. Measure actual RTO/RPO.
7. Update runbooks based on findings.

---

## Q80. How would you explain Harvester and Rancher architecture in a senior interview in two minutes?

### Model answer

“Rancher is the centralized Kubernetes management plane, while Harvester is a Kubernetes-native HCI platform for running VMs on bare metal. In Harvester, RKE2 provides orchestration, KubeVirt represents and runs VMs, Longhorn provides replicated block storage, and Multus plus bridge networking connects VMs to management or external VLAN networks. Rancher should run on its own highly available management cluster, and it can import Harvester or use the Harvester node driver to provision RKE2/K3s clusters as VMs. In production, the key concerns are quorum, failure domains, storage replica and backup design, network separation and MTU, capacity headroom for maintenance and rebuild, supported-version compatibility, least-privilege RBAC, and tested backup/restore. During troubleshooting, I correlate guest, KubeVirt, Kubernetes, Longhorn, host, and physical infrastructure evidence instead of relying only on the UI.”

---

# 10. Command Reference and Interview Checklist

## Harvester access and health

```bash
# Run on a Harvester management node using the embedded RKE2 kubeconfig
export KUBECONFIG=/etc/rancher/rke2/rke2.yaml

kubectl get nodes -o wide
kubectl get pods -A -o wide
kubectl get events -A --sort-by=.lastTimestamp | tail -100
kubectl top nodes
kubectl top pods -A
```

## VM and KubeVirt commands

```bash
kubectl get virtualmachines -A
kubectl get virtualmachineinstances -A -o wide
kubectl get virtualmachineinstancemigrations -A
kubectl get pods -A -l kubevirt.io=virt-launcher -o wide
kubectl describe vm <vm> -n <namespace>
kubectl describe vmi <vm> -n <namespace>
```

## Image, PVC, and storage commands

```bash
kubectl get virtualmachineimages -A
kubectl get storageclass
kubectl get pvc -A -o wide
kubectl get volumeattachments.storage.k8s.io
kubectl -n longhorn-system get volumes.longhorn.io
kubectl -n longhorn-system get replicas.longhorn.io
kubectl -n longhorn-system get pods -o wide
```

## Network commands

```bash
kubectl get network-attachment-definitions -A
kubectl get nodes --show-labels
nmcli device status
nmcli connection show
ip -d link show
ip -s link
bridge link
bridge vlan show
cat /proc/net/bonding/<bond-name>
ethtool <nic>
ethtool -S <nic>
tcpdump -eni <interface> 'arp or icmp'
```

## Node and operating-system commands

```bash
journalctl -u rke2-server --since '30 min ago'
journalctl -u rke2-agent --since '30 min ago'
dmesg -T | tail -200
df -h
free -h
iostat -x 1
sar -n DEV,TCP,ETCP 1
lscpu
lsmod | grep kvm
```

## Rancher commands

```bash
kubectl -n cattle-system get pods -o wide
kubectl -n cattle-system logs deploy/rancher --since=30m
kubectl -n cattle-system logs deploy/cattle-cluster-agent --since=30m
kubectl -n cattle-fleet-system get pods
kubectl get gitrepos -A
kubectl get bundles -A
kubectl get bundledeployments -A
helm -n cattle-system list
helm -n cattle-system get values rancher -o yaml
```

## API and TLS checks

```bash
curl -vk https://rancher.example.com/ping
openssl s_client \
  -connect rancher.example.com:443 \
  -servername rancher.example.com

kubectl get --raw='/readyz?verbose'
kubectl auth can-i --list --as=<user> -n <namespace>
```

## Senior interview checklist

A strong L3 answer should mention:

- Business impact and service objectives.
- Architecture and failure domains.
- Exact evidence to collect.
- Layered troubleshooting method.
- Safety/fencing before destructive storage actions.
- Backup and restore validation.
- Version/support-matrix checks.
- Change control and rollback.
- Security and least privilege.
- Monitoring and preventive actions.

---

# 11. Official References

The following official documentation should be checked for the exact release deployed in production:

- Rancher Manager documentation: <https://ranchermanager.docs.rancher.com/>
- Rancher installation and upgrade: <https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade>
- Rancher architecture recommendations: <https://ranchermanager.docs.rancher.com/reference-guides/rancher-manager-architecture/architecture-recommendations>
- Rancher backup and restore: <https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/backup-restore-and-disaster-recovery>
- SUSE Rancher Manager documentation: <https://documentation.suse.com/cloudnative/rancher-manager/latest/>
- Harvester/SUSE Virtualization documentation: <https://docs.harvesterhci.io/>
- Harvester hardware and network requirements: <https://docs.harvesterhci.io/v1.8/install/requirements>
- Harvester networking: <https://docs.harvesterhci.io/v1.8/networking/>
- Harvester VM networks: <https://docs.harvesterhci.io/v1.8/networking/harvester-network>
- Harvester StorageClasses: <https://docs.harvesterhci.io/v1.8/advanced/storageclass>
- Harvester upgrade guide: <https://docs.harvesterhci.io/v1.8/upgrade/>
- Harvester troubleshooting: <https://docs.harvesterhci.io/v1.8/troubleshooting/>
- Rancher–Harvester integration: <https://documentation.suse.com/cloudnative/virtualization/latest/en/integrations/rancher/rancher-integration.html>
- Harvester cloud provider: <https://documentation.suse.com/cloudnative/virtualization/v1.8/en/integrations/rancher/cloud-provider.html>

---

## Final L3 Principle

> **A production Harvester and Rancher engineer must be able to connect platform abstractions to physical reality.** A VM outage may originate in guest configuration, KubeVirt scheduling, a Longhorn replica, a host disk, a bond, a VLAN trunk, a certificate, a proxy, or a failed change. Senior administration means collecting evidence across those layers, choosing safe recovery actions, and preventing recurrence through architecture, automation, monitoring, and governance.
