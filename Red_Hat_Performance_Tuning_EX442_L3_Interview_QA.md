# Red Hat Performance Tuning — Corporate L3 Interview Questions and Detailed Answers

> **Target audience:** Senior Linux Administrator, RHEL Performance Engineer, Production Support L3, SRE, Platform Engineer, and RH442/EX442 candidates with substantial enterprise experience.
>
> **Primary platform:** RHEL 8 and RHEL 9. Most concepts also apply to newer RHEL releases, but always validate tunables, defaults, driver behavior, and supportability on the exact kernel and hardware in use.

---

## How to Use This Guide

A strong L3 answer should normally include five parts:

1. **Define the metric or mechanism.**
2. **Explain why it matters to the application.**
3. **Show how you would collect evidence.**
4. **Describe a safe corrective action and its trade-offs.**
5. **Explain validation, rollback, and long-term prevention.**

Performance work is not the act of changing random `sysctl` values. It is a controlled engineering process based on workload objectives, measurements, experiments, and risk management.

---

## Table of Contents

1. [Performance Engineering Fundamentals](#1-performance-engineering-fundamentals)
2. [Baseline, Monitoring, and Methodology](#2-baseline-monitoring-and-methodology)
3. [CPU and Scheduler Performance](#3-cpu-and-scheduler-performance)
4. [Memory, Swap, Reclaim, and OOM](#4-memory-swap-reclaim-and-oom)
5. [NUMA and Huge Pages](#5-numa-and-huge-pages)
6. [Storage and File-System Performance](#6-storage-and-file-system-performance)
7. [Network Performance](#7-network-performance)
8. [TuneD, Kernel Tunables, and Resource Control](#8-tuned-kernel-tunables-and-resource-control)
9. [Virtualization and Container Performance](#9-virtualization-and-container-performance)
10. [Application Profiling and Tracing](#10-application-profiling-and-tracing)
11. [Corporate Incident Scenarios](#11-corporate-incident-scenarios)
12. [Governance, Capacity, and Senior-Level Questions](#12-governance-capacity-and-senior-level-questions)
13. [Rapid-Fire Command Questions](#13-rapid-fire-command-questions)
14. [Official References](#14-official-references)

---

# 1. Performance Engineering Fundamentals

## Q1. What is performance tuning in an enterprise RHEL environment?

**Answer:**

Performance tuning is the controlled process of improving a system's ability to meet defined service objectives such as response time, throughput, concurrency, jitter, availability, and cost efficiency. It includes measurement, bottleneck identification, hypothesis formation, testing, controlled implementation, and post-change validation.

A senior engineer does not start by changing parameters. The correct sequence is:

```text
Business symptom
  -> application SLO impact
  -> system evidence
  -> bottleneck hypothesis
  -> controlled test
  -> measured improvement
  -> production rollout
  -> rollback readiness
```

In production, tuning must also preserve supportability, reliability, data integrity, security, and operational simplicity. A change that improves a benchmark by 10% but increases crash risk, recovery time, or configuration drift may be unacceptable.

Typical evidence includes:

```bash
uptime
vmstat 1
mpstat -P ALL 1
pidstat -durwt 1
sar -A
free -h
numastat
numastat -p <PID>
iostat -xz 1
ss -s
ethtool -S <interface>
perf top
```

The final proof must be application-facing: lower p95/p99 latency, higher transactions per second, fewer timeouts, or more stable service under the expected load.

---

## Q2. Explain throughput, latency, utilization, saturation, and errors.

**Answer:**

- **Throughput** is work completed per unit time, such as requests/second, MB/second, IOPS, or transactions/second.
- **Latency** is time taken to complete one operation. Enterprise analysis should examine percentiles such as p50, p95, p99, and maximum, not only averages.
- **Utilization** is the percentage of time a resource is busy. High utilization is not automatically a problem if latency and queueing remain acceptable.
- **Saturation** means work is waiting because the resource cannot immediately service it. Examples are CPU run queues, storage queue depth, TCP backlog growth, and blocked tasks.
- **Errors** include failed requests, retransmissions, packet drops, timeouts, I/O errors, allocation failures, and OOM kills.

A classic L3 mistake is assuming that high utilization always equals a bottleneck. A disk may show high `%util` while delivering excellent latency and throughput. Conversely, an application may be slow when utilization appears low because of a single-thread bottleneck, lock contention, remote NUMA access, cloud throttling, or a latency-sensitive dependency.

---

## Q3. What is the difference between performance optimization and capacity planning?

**Answer:**

Performance optimization makes the current system use resources more effectively. Capacity planning predicts when additional resources or architectural changes will be required.

Optimization asks:

- Is the workload using CPU, memory, storage, and network efficiently?
- Is a configuration causing avoidable contention?
- Can the system meet the SLO with the current hardware?

Capacity planning asks:

- What is the current peak and sustained demand?
- What is the growth rate?
- How much headroom is required for failures, maintenance, and seasonal peaks?
- When will a resource reach its safe operating threshold?

A mature organization combines both. Tuning a saturated system may postpone a hardware purchase, but tuning cannot create unlimited capacity.

---

## Q4. What is the USE method, and how do you apply it?

**Answer:**

The USE method examines every major resource for:

- **Utilization**
- **Saturation**
- **Errors**

Example mapping:

| Resource | Utilization | Saturation | Errors |
|---|---|---|---|
| CPU | `%usr`, `%sys`, per-CPU busy | run queue, throttling | machine checks, thermal events |
| Memory | used/available, working set | reclaim, swap, PSI | OOM, allocation failures |
| Disk | throughput, busy time | queue depth, await | media, path, filesystem errors |
| Network | bandwidth usage | backlog/ring/socket queues | drops, retransmits, CRC errors |

Commands:

```bash
mpstat -P ALL 1
vmstat 1
cat /proc/pressure/{cpu,memory,io}
iostat -xz 1
sar -n DEV,EDEV,TCP,ETCP 1
ethtool -S <interface>
dmesg -T | egrep -i 'error|fail|timeout|mce|thermal|oom'
```

The method prevents tunnel vision. It helps the engineer systematically prove which subsystem is constrained before making a change.

---

## Q5. Why is average latency often misleading?

**Answer:**

Averages hide outliers. For example, 99 requests may complete in 5 ms and one request in 5 seconds. The average is approximately 55 ms, which does not describe either the normal experience or the severe tail event.

For production services, tail latency often determines customer impact because one user transaction may depend on many downstream calls. A single slow dependency can increase end-to-end latency.

A strong answer should mention:

- p50 for typical behavior
- p95/p99 for user-impacting tail behavior
- maximum and timeout rate
- latency distribution or histogram
- coordinated omission in load tests
- comparison under equal load and equal data set

System-level tools such as `biolatency`, application histograms, and PCP archives help correlate tail spikes with CPU scheduling, direct reclaim, storage latency, or packet loss.

---

## Q6. What is Little's Law, and why is it useful in performance work?

**Answer:**

Little's Law states:

```text
Concurrency = Throughput × Average Response Time
```

If a service processes 2,000 requests per second at an average response time of 0.1 seconds, approximately 200 requests are in flight.

It is useful for validating observations and sizing connection pools, worker pools, queue depth, and concurrency limits. If response time increases while arrival rate remains constant, the number of in-flight requests grows. Eventually queues, memory use, and timeout risk increase.

Little's Law is not a substitute for measurement, but it is a powerful consistency check during incident analysis and capacity planning.

---

# 2. Baseline, Monitoring, and Methodology

## Q7. What must be included in a production performance baseline?

**Answer:**

A useful baseline must capture both system configuration and workload behavior.

**Configuration baseline:**

```bash
uname -a
cat /etc/redhat-release
lscpu
numactl --hardware
lsmem
lsblk -o NAME,TYPE,SIZE,ROTA,SCHED,MOUNTPOINTS
lspci -nnk
ip -s link
ethtool -k <interface>
ethtool -g <interface>
tuned-adm active
sysctl -a
systemctl list-units --type=service --state=running
```

**Performance baseline:**

- application request rate and latency percentiles
- CPU usage per CPU and per process
- run queue and context-switch rates
- memory available, page faults, reclaim, and swap
- storage latency, IOPS, throughput, and queue depth
- network throughput, drops, retransmissions, and socket counts
- cgroup throttling and pressure stall information

The baseline should cover normal load, peak load, batch windows, backup periods, month-end processing, and failure modes. A single five-minute snapshot is insufficient.

---

## Q8. How do you collect performance data without causing excessive overhead?

**Answer:**

Use a layered approach.

1. Start with low-overhead counters: PCP, `sar`, `vmstat`, `mpstat`, `iostat`, `/proc`, and application metrics.
2. Narrow the issue to a host, process, thread, device, or time window.
3. Use higher-detail tools such as `perf`, eBPF/BCC, `strace`, or `blktrace` only for the required duration and scope.

Examples:

```bash
sar -A -f /var/log/sa/sa$(date +%d)
pidstat -p <PID> -durwt 1
perf stat -p <PID> sleep 30
perf record -F 99 -g -p <PID> -- sleep 30
strace -ttT -f -p <PID> -o /tmp/trace.log
```

High-frequency tracing can alter scheduling and latency. Therefore, record the collection start/end time, frequency, process scope, tool version, and expected overhead. Never leave an unbounded trace running on a busy production server.

---

## Q9. Why is historical monitoring essential?

**Answer:**

Many incidents are intermittent and disappear before an engineer logs in. Historical data enables comparison of:

- healthy versus unhealthy periods
- before versus after deployment
- weekday versus weekend load
- current behavior versus the same business period last month
- node behavior within a cluster

RHEL commonly uses `sysstat` for `sar` archives and Performance Co-Pilot for broader metric collection, logging, and analysis.

Useful commands include:

```bash
systemctl status sysstat-collect.timer sysstat-summary.timer
sar -u -q -r -B -W -d -n DEV,TCP,ETCP
pmstat
pmrep -t 1sec kernel.all.load disk.dev.read_bytes network.interface.in.bytes
pminfo -f <metric>
```

Without history, the engineer may incorrectly treat a normal peak as abnormal or miss a regression that began days earlier.

---

## Q10. Explain how you use `vmstat 1` during triage.

**Answer:**

Important columns:

- `r`: runnable processes waiting for CPU
- `b`: tasks blocked, commonly on I/O
- `si`/`so`: swap-in and swap-out activity
- `bi`/`bo`: block input/output
- `in`: interrupts per second
- `cs`: context switches per second
- `us`: user CPU
- `sy`: kernel/system CPU
- `id`: idle CPU
- `wa`: CPU time accounted as I/O wait
- `st`: stolen time in a virtual machine

Example:

```bash
vmstat -w 1 10
```

Interpretation must be contextual. A consistently high `r` relative to available CPUs suggests CPU saturation. High `b` with rising storage latency suggests blocked I/O. Nonzero `si`/`so` during a latency incident may indicate memory pressure. High `cs` may be normal for a highly concurrent service or may indicate excessive wakeups and lock contention.

The first data line often represents an average since boot; subsequent lines represent the selected interval.

---

## Q11. What is Pressure Stall Information (PSI)?

**Answer:**

PSI reports the percentage of time tasks are delayed because CPU, memory, or I/O resources are unavailable.

```bash
cat /proc/pressure/cpu
cat /proc/pressure/memory
cat /proc/pressure/io
```

Example fields:

```text
some avg10=2.10 avg60=1.40 avg300=0.50 total=...
full avg10=0.20 avg60=0.10 avg300=0.01 total=...
```

- `some` means at least one task is stalled.
- `full` means all non-idle tasks in the relevant group are stalled simultaneously. CPU PSI generally exposes `some`, while memory and I/O can expose both.
- `avg10`, `avg60`, and `avg300` are rolling percentages.

PSI is valuable because conventional utilization metrics may look acceptable while workloads experience delays. It is especially useful with cgroups and containers to detect resource pressure at both host and workload level.

---

## Q12. How do you distinguish correlation from causation during tuning?

**Answer:**

A metric spike occurring at the same time as an incident is correlation, not proof of causation. To establish likely causation:

1. Confirm temporal alignment.
2. Confirm a plausible mechanism.
3. Reproduce the behavior under controlled conditions.
4. Change one variable at a time.
5. Observe whether the symptom changes consistently.
6. Reverse the change and verify rollback behavior when safe.

For example, high page-cache usage is not evidence of a memory problem. If application latency aligns with direct reclaim, major faults, and memory PSI, and improves after reducing working-set pressure, the causal case becomes much stronger.

---

# 3. CPU and Scheduler Performance

## Q13. Explain `%usr`, `%sys`, `%iowait`, `%steal`, and `%idle`.

**Answer:**

- `%usr`: time executing user-space code.
- `%sys`: time executing kernel code, including system calls and much kernel work.
- `%iowait`: time a CPU is idle while outstanding I/O exists. It is not a direct measurement of disk utilization.
- `%steal`: time a virtual CPU was ready but the hypervisor scheduled another workload.
- `%idle`: time with no runnable task on the CPU.

Commands:

```bash
mpstat -P ALL 1
sar -u ALL 1
pidstat -u -p ALL 1
```

High `%sys` may come from network packet processing, system-call rate, context switching, filesystem work, or interrupts. High `%iowait` suggests that tasks are waiting for I/O, but the engineer must confirm using `iostat`, process state, and storage path evidence. High `%steal` points toward host contention or oversubscription rather than a guest kernel tuning issue.

---

## Q14. What does load average mean?

**Answer:**

Linux load average approximates the number of tasks that are runnable or in uninterruptible sleep, averaged over 1, 5, and 15 minutes.

```bash
cat /proc/loadavg
uptime
```

Load is not CPU utilization. A system can have high load with low CPU use if many tasks are blocked in uninterruptible sleep, commonly due to storage, NFS, device, or kernel path delays.

A reasonable first comparison is load versus the number of logical CPUs, but this is only a starting point. A load of 16 on a 16-CPU system may be healthy for a CPU-bound batch workload but harmful for a latency-sensitive service. Examine run queue, blocked tasks, CPU distribution, and application SLOs.

---

## Q15. How do you investigate high user CPU utilization?

**Answer:**

Start broad, then narrow:

```bash
mpstat -P ALL 1
pidstat -u -p ALL 1
top -H -p <PID>
ps -eLo pid,tid,psr,pcpu,stat,comm --sort=-pcpu | head
perf top
perf record -F 99 -g -p <PID> -- sleep 30
perf report
```

Questions to answer:

- Is the load balanced across CPUs or concentrated on one core?
- Is one process or one thread responsible?
- Is the workload expected, such as batch, compression, encryption, or garbage collection?
- Is CPU frequency reduced by power policy or thermal limits?
- Is the application spinning, retrying, polling, or contending on locks?
- Did code, traffic, data volume, or compiler flags change?

The corrective action may be code optimization, workload scaling, thread-pool changes, affinity correction, a different TuneD profile, or capacity expansion. Increasing CPU quota is only appropriate if throttling is proven.

---

## Q16. How do you investigate high system CPU utilization?

**Answer:**

High system CPU means the kernel is doing substantial work. Common causes include:

- high system-call rate
- network softirq processing
- interrupt storms
- context-switch storms
- filesystem metadata activity
- page faults and reclaim
- security/audit overhead
- storage or network driver behavior

Commands:

```bash
pidstat -w -p ALL 1
pidstat -u -p ALL 1
mpstat -I ALL -P ALL 1
cat /proc/interrupts
cat /proc/softirqs
sar -w 1
perf top
perf record -a -g -- sleep 30
```

A flame graph or `perf report` can reveal kernel functions consuming CPU. The fix depends on the call path: batch tiny writes, reduce excessive logging, correct IRQ distribution, tune network queues, reduce polling, or fix an application generating unnecessary system calls.

---

## Q17. What causes high context-switch rates, and when are they a problem?

**Answer:**

Context switches occur when the scheduler changes the running task. High rates can be normal for asynchronous or highly concurrent workloads, but they become suspicious when accompanied by CPU overhead, latency, lock waits, or low useful throughput.

Causes include:

- too many threads
- very small work units
- lock/futex contention
- frequent sleep/wakeup behavior
- short time slices
- interrupt-heavy workloads
- aggressive process migration

Commands:

```bash
vmstat 1
sar -w 1
pidstat -w -t -p <PID> 1
perf sched record -- sleep 10
perf sched timehist
```

At L3 level, do not tune the scheduler first. Identify the application or kernel mechanism creating wakeups and switches. Reducing thread count, improving batching, or removing lock contention is often safer than changing global scheduler behavior.

---

## Q18. What is a single-thread bottleneck?

**Answer:**

A single-thread bottleneck occurs when one serial execution path limits throughput even though total CPU utilization is low. On a 32-CPU server, one fully utilized CPU may appear as only about 3% total CPU.

Evidence:

```bash
mpstat -P ALL 1
top -H -p <PID>
pidstat -t -u -p <PID> 1
perf top -p <PID>
```

Look for one thread at or near 100% of one CPU, a global lock, serialized garbage collection, a single acceptor thread, one busy queue, or application design that cannot parallelize.

Adding CPUs will not help unless the application can use them. Solutions may include sharding, parallelism, asynchronous I/O, lock redesign, multiple service instances, or partitioning work.

---

## Q19. What is CPU run-queue congestion?

**Answer:**

Run-queue congestion occurs when more runnable work exists than the CPUs can execute immediately. It increases scheduling delay and tail latency.

Evidence:

```bash
vmstat 1                  # r column
sar -q 1
mpstat -P ALL 1
cat /proc/pressure/cpu
pidstat -u -p ALL 1
```

A high runnable count is meaningful only relative to CPU count, workload type, and latency objectives. Also verify whether CPU quotas or cpusets restrict the workload to fewer CPUs than the host provides.

Corrective options include reducing unnecessary work, scaling out, increasing CPU allocation, correcting affinity, adjusting concurrency, removing CPU throttling, or isolating latency-sensitive workloads.

---

## Q20. Explain CPU affinity and when you would use it.

**Answer:**

CPU affinity restricts a process or thread to selected CPUs.

```bash
taskset -cp <PID>
taskset -c 2-5 <command>
ps -eLo pid,tid,psr,comm | head
```

Use cases include:

- reducing cache misses and migrations
- aligning application threads with local NUMA memory
- isolating latency-sensitive threads
- separating application threads from IRQ processing
- meeting real-time or low-jitter requirements

Risks include hot-spotting a CPU, reducing scheduler flexibility, breaking NUMA locality, and creating imbalance after hardware or workload changes. Affinity should be documented and validated with per-CPU utilization, migration rate, cache behavior, and application latency.

---

## Q21. Explain nice values, real-time policies, and `chrt`.

**Answer:**

Normal tasks typically use CFS scheduling policies such as `SCHED_OTHER`. Nice values influence relative CPU weight; lower numeric nice values receive higher priority.

```bash
nice -n 10 <command>
renice -n 5 -p <PID>
chrt -p <PID>
```

Real-time policies include `SCHED_FIFO` and `SCHED_RR`. They can preempt normal tasks and can starve essential system services if misused.

```bash
chrt -f 50 <command>   # SCHED_FIFO
chrt -r 50 <command>   # SCHED_RR
```

An L3 engineer treats real-time priority as a controlled exception. The process must be bounded, tested, monitored, and assigned suitable CPU affinity. Watchdog, logging, storage, network, and housekeeping threads must retain enough CPU to keep the system manageable.

---

## Q22. How do CPU frequency scaling and thermal throttling affect performance?

**Answer:**

Modern CPUs change frequency based on power policy, load, temperature, and platform limits. A CPU can show high utilization but deliver lower-than-expected work if frequency is reduced.

Commands:

```bash
lscpu
cpupower frequency-info
cpupower monitor
turbostat --interval 1
sensors
journalctl -k | egrep -i 'thermal|thrott|temperature'
tuned-adm active
```

For latency-sensitive workloads, deep C-states and frequency transitions may increase jitter. For throughput workloads, power-saving policies may reduce sustained performance. However, disabling power management increases power use and heat and may trigger thermal throttling, which can make performance worse.

The correct policy depends on workload objectives, server firmware, cooling, and data-center constraints.

---

## Q23. How do you identify CPU cgroup throttling?

**Answer:**

A workload may be throttled even when host CPUs are idle because its cgroup has a CPU quota or weight constraint.

For cgroup v2:

```bash
cat /sys/fs/cgroup/<path>/cpu.max
cat /sys/fs/cgroup/<path>/cpu.stat
systemctl show <service> -p CPUQuotaPerSecUSec -p CPUWeight -p AllowedCPUs
```

Important `cpu.stat` values can include usage and throttling counters. For containers, inspect the runtime and orchestration limits as well.

Symptoms include periodic latency, a process that cannot exceed a fixed CPU share, rising throttled time, and idle CPUs outside the allowed cpuset.

The fix may be to increase quota, correct requests/limits, rebalance cpusets, reduce concurrency, or optimize the application. Removing all limits can create noisy-neighbor risk.

---

# 4. Memory, Swap, Reclaim, and OOM

## Q24. Why is low `free` memory not automatically a problem?

**Answer:**

Linux intentionally uses otherwise idle memory for page cache, inode/dentry caches, and other reclaimable data. Therefore, `free` memory alone is a poor indicator.

```bash
free -h
cat /proc/meminfo
vmstat 1
```

Focus on:

- `MemAvailable`
- active and inactive anonymous/file pages
- swap activity
- reclaim behavior
- major page faults
- memory PSI
- OOM events
- application working-set behavior

If `MemAvailable` is healthy and there is no harmful reclaim, swap churn, or OOM activity, high memory usage may be beneficial because it reduces storage reads.

---

## Q25. Explain page cache and its performance impact.

**Answer:**

The page cache stores file data in RAM. Reads can be served from memory instead of storage, and buffered writes can be accumulated before writeback.

Benefits:

- lower read latency
- reduced physical I/O
- better write batching
- improved application throughput

Risks under pressure:

- dirty-page buildup can cause writeback bursts
- excessive cache churn can increase major faults
- direct reclaim can stall application threads
- cache-heavy workloads can compete with anonymous memory

Useful tools:

```bash
free -h
cat /proc/meminfo
sar -B 1
vmstat 1
vmtouch -v <file-or-directory>
mincore-related application tools
```

Do not clear caches as a routine tuning action. Dropping caches changes workload behavior and can cause a severe read storm. It is mainly useful in controlled testing.

---

## Q26. What is the difference between minor and major page faults?

**Answer:**

A **minor fault** occurs when the required page can be mapped without reading it from storage. Examples include copy-on-write or a page already present in memory.

A **major fault** requires storage access to obtain the page. Major faults are much more likely to affect latency.

```bash
pidstat -r -p <PID> 1
sar -B 1
perf stat -e page-faults,minor-faults,major-faults -p <PID> sleep 30
```

High minor-fault rates are not automatically harmful. High major-fault rates during latency spikes may indicate insufficient cache, memory pressure, memory-mapped file access, application startup, or working-set growth.

---

## Q27. Explain memory reclaim, `kswapd`, and direct reclaim.

**Answer:**

When free memory falls below watermarks, the kernel reclaims pages.

- **Background reclaim** is performed mainly by `kswapd`.
- **Direct reclaim** occurs when an allocating task must reclaim memory itself before its allocation can proceed.

Direct reclaim is especially important because it directly stalls application execution and can create tail-latency spikes.

Evidence:

```bash
vmstat 1
sar -B 1
cat /proc/vmstat | egrep 'pgscan|pgsteal|allocstall|compact'
cat /proc/pressure/memory
perf top
```

Corrective actions may include reducing working-set size, correcting memory limits, adding memory, tuning application caches, controlling concurrency, addressing a leak, or adjusting NUMA placement. Global VM tunables should be changed only with controlled evidence.

---

## Q28. What is swap, and should swap always be disabled?

**Answer:**

Swap provides backing storage for anonymous pages and can help the kernel move cold anonymous memory out of RAM. It can preserve useful page cache and provide operational headroom.

Disabling swap is not universally correct. It may reduce latency variability for selected workloads, but it also reduces memory-management flexibility and may cause earlier OOM kills.

Commands:

```bash
swapon --show
free -h
vmstat 1
sar -W 1
cat /proc/<PID>/status | grep -i swap
smem -rs swap
```

The important question is not whether swap is configured, but whether there is harmful swap churn. Sustained `si`/`so`, major faults, high memory PSI, and latency impact indicate pressure. A small amount of old data in swap with no active paging may be harmless.

---

## Q29. What does `vm.swappiness` control?

**Answer:**

`vm.swappiness` influences the kernel's relative preference for reclaiming anonymous memory versus file-backed cache. It does not represent a percentage of RAM at which swapping starts.

```bash
sysctl vm.swappiness
sysctl -w vm.swappiness=<tested-value>
```

A lower value generally makes anonymous swapping less attractive; a higher value makes it more acceptable. The correct value depends on the workload. Database systems with their own cache may prefer a lower value, while general-purpose systems may benefit from retaining more file cache.

Changes must be validated using swap I/O, page faults, page-cache hit behavior, memory PSI, and application latency. Setting swappiness to zero does not guarantee that swapping can never occur.

---

## Q30. How do you troubleshoot a suspected application memory leak?

**Answer:**

First distinguish between:

- process RSS growth
- virtual address-space growth
- page-cache growth
- shared memory
- kernel slab growth
- expected application cache growth

Commands:

```bash
ps -o pid,vsz,rss,etimes,cmd -p <PID>
pmap -x <PID>
cat /proc/<PID>/smaps_rollup
pidstat -r -p <PID> 10
smem -tk
systemd-cgtop
```

Trend memory against request volume, object count, cache entries, and uptime. A leak typically shows unbounded growth that does not return after load decreases.

Application-level tools may include heap profilers, JVM native memory tracking, Valgrind/Massif in non-production, language-specific profilers, and allocation tracing. The production mitigation may be a controlled restart or tighter limit, but the permanent fix is in the application.

---

## Q31. How do you troubleshoot kernel slab-memory growth?

**Answer:**

Slab memory is used for kernel objects such as dentries, inodes, network structures, and filesystem metadata.

```bash
slabtop -o
cat /proc/slabinfo
cat /proc/meminfo | egrep 'Slab|SReclaimable|SUnreclaim'
```

Identify which cache is growing and correlate it with workload changes. Examples include:

- dentry/inode growth from millions of files
- network object growth from connection churn
- filesystem or driver leaks
- unreclaimed kernel objects

Do not assume all slab is leaked. `SReclaimable` can often be reclaimed under pressure. Persistent `SUnreclaim` growth combined with kernel symptoms may require kernel/driver investigation and Red Hat support data.

---

## Q32. Explain the Linux OOM killer and how you investigate an OOM event.

**Answer:**

When the kernel cannot satisfy memory allocations and reclaim cannot make sufficient progress, it may invoke the OOM killer. Selection is influenced by memory usage and `oom_score_adj`, among other factors.

```bash
journalctl -k -g 'Out of memory|Killed process|oom-kill'
dmesg -T | egrep -i 'oom|out of memory|killed process'
cat /proc/<PID>/oom_score
cat /proc/<PID>/oom_score_adj
```

Determine whether it was:

- global host OOM
- cgroup/service/container OOM
- NUMA-local allocation failure
- huge-page allocation failure
- memory-limit enforcement

Collect workload memory, limits, swap, reclaim, PSI, and the OOM log. Never "fix" an OOM only by protecting one process with a very negative `oom_score_adj`; that can cause a more important process or the entire host to fail instead.

---

## Q33. What is memory overcommit?

**Answer:**

Memory overcommit controls how the kernel accounts for virtual memory commitments.

```bash
sysctl vm.overcommit_memory
sysctl vm.overcommit_ratio
cat /proc/meminfo | egrep 'CommitLimit|Committed_AS'
```

Common modes:

- `0`: heuristic overcommit
- `1`: allow overcommit more freely
- `2`: stricter accounting based on commit limit

Virtual allocation does not always equal physical use because applications may reserve address space without touching every page. Strict accounting can prevent allocations earlier, while permissive overcommit can allow allocations that later contribute to OOM.

The setting must match application behavior and reliability requirements. Database and in-memory workloads require careful testing and vendor guidance.

---

# 5. NUMA and Huge Pages

## Q34. What is NUMA, and why does it matter?

**Answer:**

In a NUMA system, memory access time depends on whether the memory is attached to the local CPU socket/node or a remote node. Remote access consumes interconnect bandwidth and usually has higher latency.

Commands:

```bash
lscpu
numactl --hardware
numastat
numastat -p <PID>
cat /proc/<PID>/numa_maps
```

NUMA problems often appear after:

- moving to a larger multi-socket server
- incorrect VM vCPU topology
- process pinning without memory binding
- one NUMA node running out of memory
- application threads migrating between nodes

A senior answer must connect CPU placement and memory placement. Pinning CPU threads without ensuring local memory can make performance worse.

---

## Q35. How do you use `numactl` for testing?

**Answer:**

Examples:

```bash
numactl --cpunodebind=0 --membind=0 <command>
numactl --physcpubind=0-7 --membind=0 <command>
numactl --interleave=all <command>
numactl --preferred=1 <command>
```

- `--membind` strictly allocates from selected nodes and can fail if memory is unavailable.
- `--preferred` prefers a node but may fall back.
- `--interleave` spreads allocations across nodes and can help bandwidth-oriented workloads.

Use controlled A/B tests with equal load, warm-up, data set, and CPU frequency conditions. Measure local/remote allocations, memory bandwidth, LLC misses, throughput, and p99 latency.

---

## Q36. Explain Transparent Huge Pages versus explicit HugeTLB pages.

**Answer:**

Huge pages reduce page-table entries and Translation Lookaside Buffer pressure.

**Transparent Huge Pages (THP):**

- managed automatically by the kernel
- can improve some workloads
- may introduce allocation, compaction, or split/collapse latency

**Explicit HugeTLB pages:**

- pre-reserved or explicitly managed
- predictable for applications designed to use them
- reduce flexible memory availability
- require capacity planning and application configuration

Commands:

```bash
cat /sys/kernel/mm/transparent_hugepage/enabled
cat /sys/kernel/mm/transparent_hugepage/defrag
grep -i huge /proc/meminfo
cat /proc/<PID>/smaps_rollup | egrep 'AnonHugePages|ShmemPmdMapped|FilePmdMapped'
```

Do not disable THP globally only because of a generic recommendation. Confirm compaction stalls, application guidance, and before/after latency under realistic load.

---

## Q37. Why can huge-page allocation fail even when free memory exists?

**Answer:**

A large physically contiguous page may not be available because memory is fragmented. Total free memory can be sufficient while no contiguous block of the required order exists.

Evidence:

```bash
cat /proc/buddyinfo
cat /proc/pagetypeinfo
grep -i compact /proc/vmstat
grep -i huge /proc/meminfo
```

Possible approaches:

- reserve huge pages at boot
- allocate before memory becomes fragmented
- reduce long-term fragmentation
- use application-aware huge-page configuration
- schedule a reboot for deterministic reservation when justified

For NUMA systems, reserve pages on the intended nodes and validate local availability.

---

# 6. Storage and File-System Performance

## Q38. Explain the important fields in `iostat -xz 1`.

**Answer:**

Fields vary by sysstat version, but common indicators include:

- `r/s`, `w/s`: read/write operations per second
- `rkB/s`, `wkB/s`: throughput
- `r_await`, `w_await`: average read/write completion time including queueing
- `aqu-sz`: average queue size
- `rareq-sz`, `wareq-sz`: average request size
- `%util`: percentage of time the device had I/O in progress

```bash
iostat -xz 1
```

Interpret device type correctly. `%util` is not a universal saturation metric for modern NVMe, RAID, SAN, or virtual devices with parallel queues. Focus on application latency, device latency distribution, queue growth, service-time behavior, throughput/IOPS limits, and errors.

Map logical devices through LVM, device mapper, multipath, RAID, hypervisor, and storage array layers before blaming one device name.

---

## Q39. What is I/O wait, and why is it often misunderstood?

**Answer:**

`%iowait` is CPU idle time during which at least one I/O request is outstanding. It is a CPU accounting category, not the percentage of time a disk is slow.

High iowait can indicate blocked application I/O, but it must be correlated with:

```bash
iostat -xz 1
pidstat -d -p ALL 1
vmstat 1
ps -eo state,pid,wchan:32,comm | awk '$1 ~ /D/'
```

Low iowait does not prove storage is healthy. If the workload is CPU busy while some threads wait for storage, iowait may remain low. Asynchronous applications may also hide waiting. Always use end-to-end latency and per-device evidence.

---

## Q40. How do you troubleshoot high storage latency?

**Answer:**

Use a stack-oriented workflow:

1. Confirm application I/O latency and affected operations.
2. Identify processes and files.
3. Map filesystem to logical volume, multipath device, physical path, and array/LUN.
4. Check queue depth, IOPS, throughput, request size, and read/write mix.
5. Check errors, retries, path failures, and throttling.
6. Compare with baseline and storage-side telemetry.

Commands:

```bash
iostat -xz 1
pidstat -d -p ALL 1
iotop -oPa
lsblk -o NAME,TYPE,SIZE,ROTA,SCHED,MOUNTPOINTS
findmnt
lvs -a -o +devices
multipath -ll
journalctl -k | egrep -i 'blk|scsi|nvme|timeout|reset|error'
```

For latency distributions and process attribution:

```bash
/usr/share/bcc/tools/biolatency
/usr/share/bcc/tools/biosnoop
```

Averages can hide outliers; histogram-based evidence is preferred.

---

## Q41. How do you select an I/O scheduler?

**Answer:**

There is no single best scheduler for every device and workload. Selection depends on:

- device type and driver
- rotational versus solid-state storage
- multi-queue behavior
- host versus guest
- throughput versus latency goal
- application I/O pattern

```bash
cat /sys/block/<device>/queue/scheduler
lsblk -o NAME,ROTA,SCHED
```

Common modern schedulers can include `none`, `mq-deadline`, `bfq`, or others depending on the kernel and device.

The correct interview answer is: use supported defaults as the starting point, verify the effective scheduler on the actual device, benchmark the representative workload, monitor tail latency and throughput, and document rollback. A scheduler change that helps one workload may hurt another sharing the device.

---

## Q42. Explain read-ahead and when it helps or hurts.

**Answer:**

Read-ahead prefetches blocks that the kernel expects an application to read soon.

```bash
blockdev --getra /dev/<device>
blockdev --setra <sectors> /dev/<device>
cat /sys/block/<device>/queue/read_ahead_kb
```

It can help large sequential reads by reducing request overhead and keeping storage busy. It can hurt random workloads by reading unused data, consuming bandwidth, polluting cache, and increasing latency for useful I/O.

Tune at the correct layer and validate with cache hit behavior, request size, throughput, device latency, and application performance.

---

## Q43. Explain dirty pages and writeback tuning.

**Answer:**

Buffered writes first modify pages in memory. These dirty pages are later written to storage by kernel writeback threads or by application-triggered pressure.

Relevant settings include:

```bash
sysctl vm.dirty_background_ratio
sysctl vm.dirty_ratio
sysctl vm.dirty_background_bytes
sysctl vm.dirty_bytes
sysctl vm.dirty_expire_centisecs
sysctl vm.dirty_writeback_centisecs
```

Large dirty limits can improve burst throughput but may create long writeback stalls, large recovery exposure, and latency spikes. Very small limits can cause continuous writeback and reduce batching efficiency.

Use bytes-based controls for predictable behavior on systems with different RAM sizes. Change only after measuring dirty-page growth, writeback, device latency, and application fsync behavior.

---

## Q44. How does `fsync()` affect application performance?

**Answer:**

`fsync()` asks the system to flush required file data and metadata so that it reaches stable storage according to the storage stack's guarantees. It can be expensive because the application may wait for device completion.

Investigate with:

```bash
strace -ttT -f -e trace=fsync,fdatasync,sync_file_range -p <PID>
perf trace -p <PID>
/usr/share/bcc/tools/fileslower
```

A slow `fsync()` can result from storage latency, cache policy, writeback congestion, filesystem journaling, virtualized storage, or application behavior such as syncing every small record.

Do not disable durability controls to improve a benchmark unless the business explicitly accepts data-loss risk. Better solutions include batching, group commit, faster durable storage, correct cache protection, and database-specific tuning.

---

## Q45. What XFS factors are important for performance?

**Answer:**

XFS performance depends on workload, filesystem geometry, allocation groups, inode behavior, log design, mount options, fragmentation, and underlying storage.

Useful commands:

```bash
xfs_info <mountpoint>
xfs_db -r -c frag -c quit /dev/<device>
xfs_spaceman -c 'health' <mountpoint>
xfs_io -c stat <file>
xfs_repair -n /dev/<device>   # offline/read-only assessment rules apply
```

Important principles:

- create the filesystem with geometry appropriate for the storage layout
- avoid unnecessary mount-option tuning
- use preallocation for large growing files when appropriate
- monitor inode and metadata workloads
- use batch discard (`fstrim`) as a common starting approach rather than indiscriminate continuous discard
- preserve data integrity and supported configurations

Never run repair commands casually on a mounted production filesystem.

---

## Q46. How do LVM, RAID, SAN, and multipath layers complicate troubleshooting?

**Answer:**

An application sees a filesystem, but latency may originate at any layer:

```text
File -> filesystem -> logical volume -> device mapper -> multipath/RAID
     -> HBA/NIC -> fabric -> storage controller -> physical media
```

Commands:

```bash
findmnt -T <path>
lsblk -f
pvs -o +pv_used
vgs
lvs -a -o +devices,segtype
multipath -ll
cat /proc/mdstat
mdadm --detail /dev/mdX
```

Check for path degradation, uneven path use, RAID rebuild, thin-pool pressure, snapshots, cache mode, queue limits, array-side throttling, and alignment.

A senior engineer coordinates host and storage teams using synchronized timestamps and LUN/device identifiers. Host-only metrics cannot always prove array-side root cause.

---

## Q47. How do you benchmark storage correctly with `fio`?

**Answer:**

A valid `fio` test must match the production I/O pattern:

- random versus sequential
- read/write ratio
- block size
- queue depth
- number of jobs
- buffered versus direct I/O
- synchronous versus asynchronous engine
- working-set size larger than cache when testing media
- runtime long enough to reach steady state
- durability requirements

Example:

```bash
fio --name=randread \
    --filename=/test/fio.dat \
    --size=20G --bs=4k --rw=randread \
    --ioengine=libaio --direct=1 \
    --iodepth=32 --numjobs=4 \
    --runtime=300 --time_based \
    --group_reporting
```

Never run destructive tests against production devices. Record hardware, filesystem, cache state, background load, firmware, kernel, and test parameters. Compare latency percentiles, not only IOPS.

---

# 7. Network Performance

## Q48. How do you approach a network performance incident?

**Answer:**

Start by defining the symptom: connection setup delay, packet loss, low throughput, high application latency, retransmissions, or drops.

Then inspect layers:

```bash
ip -s link
ethtool <interface>
ethtool -S <interface>
sar -n DEV,EDEV,TCP,ETCP 1
ss -s
nstat -az
ip route get <destination>
tracepath <destination>
ping -M do -s <size> <destination>
```

Also check application socket behavior, DNS, TLS, load balancers, firewalls, conntrack, route asymmetry, MTU, switch counters, and remote endpoint health.

A ping test alone does not prove application connectivity or performance. ICMP may take a different policy path and does not test TCP accept queues, TLS, or application processing.

---

## Q49. What causes TCP retransmissions?

**Answer:**

Common causes include:

- packet loss or corruption
- congestion
- receiver or sender queue overflow
- NIC ring drops
- MTU/PMTU issues
- duplex/link problems
- overloaded network stack
- path asymmetry or firewall behavior
- remote endpoint overload

Commands:

```bash
sar -n TCP,ETCP 1
nstat -az | egrep 'Retrans|Timeout|Listen|Prune'
ss -ti
ethtool -S <interface>
ip -s link
```

Retransmissions increase latency and reduce throughput because TCP interprets loss as a congestion signal. Determine whether loss is local, in the network, or remote. Packet captures should be collected at more than one point when possible.

---

## Q50. Explain bandwidth-delay product and TCP socket buffers.

**Answer:**

Bandwidth-delay product estimates the amount of data required in flight to fully utilize a path:

```text
BDP = Bandwidth × Round-trip time
```

A 10 Gbit/s path with 50 ms RTT has a large BDP. The TCP window and application behavior must permit enough data in flight.

Relevant settings:

```bash
sysctl net.ipv4.tcp_rmem
sysctl net.ipv4.tcp_wmem
sysctl net.core.rmem_max
sysctl net.core.wmem_max
sysctl net.ipv4.tcp_window_scaling
ss -ti
```

Do not simply set enormous global buffers. Oversized buffers can increase memory use and bufferbloat. Modern TCP autotuning should be respected; change maxima only when measurement proves a limit and the workload requires it.

---

## Q51. What are NIC ring buffers and kernel backlog queues?

**Answer:**

NIC RX/TX rings hold packets between hardware and the driver. Kernel backlog queues hold received packets before the protocol stack processes them.

Commands:

```bash
ethtool -g <interface>
ethtool -S <interface>
sysctl net.core.netdev_max_backlog
cat /proc/net/softnet_stat
```

Drops can occur when packets arrive faster than the system drains these queues. Possible causes include insufficient ring size, overloaded SoftIRQs, poor IRQ distribution, inadequate CPU capacity, or bursty traffic.

Increasing queues may absorb bursts but also increases memory use and queueing latency. Fix the processing bottleneck rather than only making queues larger.

---

## Q52. Explain IRQ, SoftIRQ, RSS, RPS, and XPS.

**Answer:**

- **IRQ:** hardware interrupt signaling the CPU.
- **SoftIRQ:** deferred kernel work, including network processing.
- **RSS:** NIC hardware distributes receive queues across CPUs.
- **RPS:** software receive packet steering.
- **XPS:** transmit packet steering.

Commands:

```bash
cat /proc/interrupts
cat /proc/softirqs
mpstat -I ALL -P ALL 1
ethtool -l <interface>
ethtool -x <interface>
systemctl status irqbalance
```

The goal is to distribute packet processing without destroying cache and NUMA locality. For high-throughput systems, align queues, IRQs, application threads, and local memory. For low latency, avoid unnecessary cross-CPU processing and queueing.

Changes must account for `irqbalance`, which may overwrite manual affinity decisions unless configured appropriately.

---

## Q53. When should NIC offload features be changed?

**Answer:**

Offloads such as checksum offload, TSO, GSO, GRO, and LRO reduce CPU work by combining or delegating packet processing.

```bash
ethtool -k <interface>
ethtool -K <interface> gro off
```

Most offloads should remain enabled unless:

- troubleshooting proves a driver/offload problem
- packet capture requirements demand temporary changes
- a specific workload, such as some high-rate UDP patterns, performs worse
- vendor guidance applies

Disabling offloads can dramatically increase CPU and interrupt load. Test throughput, latency, drops, and CPU before and after. Persistent settings should be managed through supported NetworkManager configuration where applicable.

---

## Q54. How do you troubleshoot listen-queue overflow?

**Answer:**

Listen-queue overflow occurs when incoming connection attempts exceed the application's ability to accept them or configured queue limits.

Evidence:

```bash
ss -lnt
ss -s
nstat -az | egrep 'ListenOverflows|ListenDrops|Syncookies'
sysctl net.core.somaxconn
sysctl net.ipv4.tcp_max_syn_backlog
```

Possible root causes:

- slow accept loop
- insufficient application workers
- CPU throttling
- dependency delay
- too-small application backlog
- SYN flood or traffic spike

Increase backlog only after confirming that the application can actually process the additional connections. Otherwise, a larger queue only delays failure and consumes more resources.

---

## Q55. Explain ephemeral-port exhaustion and excessive `TIME_WAIT`.

**Answer:**

Clients use ephemeral source ports for outbound connections. Heavy short-lived connection churn to the same destination can exhaust the available tuple space.

```bash
sysctl net.ipv4.ip_local_port_range
ss -s
ss -tan state time-wait | wc -l
ss -tan | awk '{print $1}' | sort | uniq -c
```

Root causes often include:

- no connection pooling or keepalive
- retry storms
- very high fan-out
- NAT constraints
- narrow port range

The preferred fix is application connection reuse, sensible timeouts, backoff, and scaling of source IPs/NAT capacity where required. Blindly reducing TCP timers can create correctness and network problems.

---

## Q56. How do you identify an MTU mismatch?

**Answer:**

Symptoms include connections that establish but stall on larger transfers, VPN/tunnel problems, and selective application failures.

Commands:

```bash
ip link show
ip route get <destination>
tracepath <destination>
ping -M do -s 1472 <destination>   # example for IPv4 over MTU 1500
```

Account for encapsulation overhead from VLANs, VXLAN, GRE, IPsec, cloud overlays, and virtualization. Path MTU discovery can fail when ICMP "fragmentation needed" messages are blocked.

Fix MTU consistently across the path or restore proper PMTU discovery. Do not assume jumbo frames improve performance unless every hop supports them and the workload benefits.

---

# 8. TuneD, Kernel Tunables, and Resource Control

## Q57. What is TuneD, and why is it preferred over ad hoc tuning?

**Answer:**

TuneD is RHEL's profile-based system tuning service. It applies coordinated settings for workload categories such as throughput, latency, virtualization hosts, and virtual guests.

```bash
systemctl status tuned
tuned-adm list
tuned-adm active
tuned-adm recommend
tuned-adm verify
tuned-adm profile throughput-performance
```

TuneD is preferable to scattered boot scripts and manual commands because it provides:

- consistent profile management
- predefined supported starting points
- inheritance and custom profiles
- repeatability across hosts
- easier audit and rollback

However, a recommended profile is a starting point, not proof that it is optimal. Validate application performance, power impact, latency, and side effects.

---

## Q58. How do you create a custom TuneD profile safely?

**Answer:**

Create a profile under `/etc/tuned/<profile-name>/tuned.conf` and inherit from a suitable base profile.

Example:

```ini
[main]
include=throughput-performance
summary=Corporate application throughput profile

[sysctl]
# Add only tested settings with documented ownership.
vm.swappiness=10

[vm]
transparent_hugepages=madvise
```

Then:

```bash
tuned-adm profile <profile-name>
tuned-adm active
tuned-adm verify
```

Corporate controls should include:

- business and technical justification
- supported RHEL/kernel scope
- hardware/workload eligibility rules
- benchmark evidence
- configuration owner
- rollback profile
- exceptions and expiration review

Avoid copying a large internet tuning template. Every parameter must have a measured purpose.

---

## Q59. How do you make `sysctl` changes safely and persistently?

**Answer:**

First inspect the current value and documentation:

```bash
sysctl <key>
sysctl -d <key>
```

Test temporarily:

```bash
sysctl -w <key>=<value>
```

Persist in a clearly named file such as:

```text
/etc/sysctl.d/60-corporate-performance.conf
```

Apply and verify:

```bash
sysctl --system
sysctl <key>
```

The change record must state scope, expected result, risk, test evidence, and rollback value. Be aware that TuneD, boot parameters, container runtime settings, or application startup scripts may also modify the same setting.

---

## Q60. Explain systemd resource controls for CPU, memory, and I/O.

**Answer:**

systemd maps service controls to cgroups. Common properties include:

```ini
[Service]
CPUWeight=200
CPUQuota=400%
AllowedCPUs=0-7
MemoryHigh=8G
MemoryMax=10G
IOWeight=200
TasksMax=4096
```

Inspect effective values:

```bash
systemctl show <service> | egrep 'CPU|Memory|IO|Tasks|Allowed'
systemd-cgtop
systemctl status <service>
```

Key distinction:

- weights distribute resources during contention
- quotas/maximums impose hard ceilings
- `MemoryHigh` is a pressure/throttling boundary
- `MemoryMax` is a hard limit that may lead to cgroup OOM

Limits protect the platform but can also create hidden throttling. Capacity and SLOs must be considered together.

---

## Q61. What is the difference between `ulimit`, `prlimit`, and systemd limits?

**Answer:**

`ulimit` is a shell built-in that displays or sets process resource limits for the shell and its children.

```bash
ulimit -a
ulimit -n 65536
```

`prlimit` inspects or changes limits for a command or running process where permitted.

```bash
prlimit --pid <PID>
prlimit --nofile=65536:65536 --pid <PID>
```

For systemd services, use unit settings such as:

```ini
LimitNOFILE=65536
LimitNPROC=8192
```

Do not rely only on `/etc/security/limits.conf` for system services. PAM limits commonly affect login sessions, while systemd units have their own configured limits.

---

# 9. Virtualization and Container Performance

## Q62. What is CPU steal time in a virtual machine?

**Answer:**

Steal time is time when a guest vCPU is ready to run but the hypervisor schedules another workload instead.

```bash
mpstat -P ALL 1
vmstat 1
sar -u ALL 1
```

Persistent steal time suggests host contention, vCPU overcommit, host scheduling pressure, or noisy neighbors. Guest tuning cannot create CPU time that the hypervisor does not provide.

Investigate:

- host CPU oversubscription
- vCPU count and topology
- pinning and NUMA placement
- competing guests
- cloud instance throttling or credits
- maintenance/migration events

A very large VM can perform worse than a correctly sized VM if its vCPUs cannot be scheduled efficiently.

---

## Q63. How do you tune a KVM virtual machine for NUMA locality?

**Answer:**

The goal is to align guest vCPUs, emulator threads, memory, and I/O processing with host NUMA topology.

Useful host tools:

```bash
virsh vcpuinfo <vm>
virsh vcpupin <vm>
virsh emulatorpin <vm>
virsh numatune <vm>
numastat -p <qemu-pid>
```

Consider:

- guest vCPU topology matching physical topology
- vCPU pinning for predictable workloads
- emulator and I/O thread placement
- huge-page availability on the selected NUMA node
- memory backing and node binding
- NIC and storage device locality

Aggressive pinning may reduce live-migration flexibility and can harm other guests. This trade-off must be approved and documented.

---

## Q64. What TuneD profiles are commonly considered for virtualization?

**Answer:**

Typical RHEL starting points include:

```bash
tuned-adm profile virtual-host    # virtualization host
tuned-adm profile virtual-guest   # virtual machine guest
```

The effective recommendation depends on release and workload:

```bash
tuned-adm recommend
tuned-adm verify
```

A profile should not replace workload testing. A virtualization host may carry mixed latency, throughput, and overcommit requirements. In some cases, a custom inherited profile is more appropriate.

---

## Q65. How do container CPU limits cause latency even when the host is idle?

**Answer:**

A container can be limited by CFS bandwidth/quota or cpuset restrictions. Once it consumes its quota for the period, its tasks are throttled until the next period, even if other CPUs are idle.

Evidence includes:

- container CPU throttling metrics
- cgroup `cpu.stat`
- orchestration CPU requests and limits
- periodic latency spikes
- idle host CPU outside the allowed cpuset

Commands vary by runtime, but host-level checks include:

```bash
systemd-cgtop
cat /sys/fs/cgroup/<container-cgroup>/cpu.max
cat /sys/fs/cgroup/<container-cgroup>/cpu.stat
cat /sys/fs/cgroup/<container-cgroup>/cpuset.cpus.effective
```

Corrective action may involve changing requests/limits, scaling replicas, reducing concurrency, or optimizing the workload. Remove limits only after evaluating noisy-neighbor and capacity risk.

---

## Q66. How do memory limits behave for containers and services?

**Answer:**

A workload can experience reclaim, throttling, or cgroup OOM while the host still has available memory because the cgroup limit is enforced independently.

Check:

```bash
cat /sys/fs/cgroup/<path>/memory.current
cat /sys/fs/cgroup/<path>/memory.high
cat /sys/fs/cgroup/<path>/memory.max
cat /sys/fs/cgroup/<path>/memory.events
cat /sys/fs/cgroup/<path>/memory.pressure
```

Important distinctions:

- host memory pressure versus cgroup-local pressure
- working set versus page cache
- `memory.high` pressure versus `memory.max` hard limit
- application OOM versus kernel/cgroup OOM

Tune the application and limits together. An unrealistically low limit can cause cache churn and latency long before an OOM kill occurs.

---

# 10. Application Profiling and Tracing

## Q67. What is the difference between `perf stat`, `perf top`, and `perf record`?

**Answer:**

- `perf stat` provides aggregate counters for a command, process, or system.
- `perf top` shows live hot functions.
- `perf record` samples events and stores data for later analysis with `perf report`.

Examples:

```bash
perf stat -p <PID> sleep 30
perf top -p <PID>
perf record -F 99 -g -p <PID> -- sleep 30
perf report
```

Useful counters can include cycles, instructions, cache misses, branches, and context switches. Interpretation requires understanding sampling frequency, symbol availability, debug information, JIT behavior, and virtualization support.

A high cache-miss rate is not automatically a root cause; correlate it with stalled cycles, memory bandwidth, code path, and application throughput.

---

## Q68. When would you use `strace`, and what are its risks?

**Answer:**

`strace` shows system calls, arguments, return values, and timing. It is useful for identifying:

- blocking reads/writes
- slow `fsync()`
- repeated failed file lookups
- connection timeouts
- excessive polling or retries
- futex waits

```bash
strace -ttT -f -p <PID> -o /tmp/trace.log
strace -c -p <PID>
```

Risks include overhead, altered timing, large output, and exposure of sensitive arguments or paths. Scope the trace by PID, syscall set, and duration.

```bash
strace -ttT -f -e trace=network,read,write,fsync,futex -p <PID>
```

For high-rate production workloads, sampling with `perf` or eBPF may be less intrusive.

---

## Q69. How do eBPF/BCC tools help performance troubleshooting?

**Answer:**

They provide dynamic, often low-overhead observability inside kernel and application paths without rebuilding the kernel.

Examples commonly available through BCC packages include:

```bash
/usr/share/bcc/tools/biolatency
/usr/share/bcc/tools/biosnoop
/usr/share/bcc/tools/tcpconnect
/usr/share/bcc/tools/tcpretrans
/usr/share/bcc/tools/runqlat
/usr/share/bcc/tools/offcputime
/usr/share/bcc/tools/execsnoop
```

Benefits:

- latency histograms
- process attribution
- event-level tracing
- visibility into off-CPU time

Risks and constraints:

- kernel/BTF compatibility
- privileges
- overhead at very high event rates
- data-volume and privacy concerns
- need for correct interpretation

Use a tested runbook and time-bounded collection in production.

---

## Q70. What is off-CPU analysis?

**Answer:**

On-CPU profiling shows where a thread spends CPU time. Off-CPU analysis shows why a thread is not running, such as:

- storage wait
- network wait
- lock/futex contention
- sleep/timer wait
- scheduler delay
- memory reclaim

Useful tools:

```bash
pidstat -w -t -p <PID> 1
perf sched timehist
/usr/share/bcc/tools/offcputime -p <PID> 10
strace -ttT -f -p <PID>
```

This is essential when CPU utilization is low but response time is high. The bottleneck may be waiting rather than executing.

---

# 11. Corporate Incident Scenarios

## Q71. Production latency increased immediately after a kernel update. What do you do?

**Answer:**

1. Confirm exact kernel, boot time, deployment time, firmware, and workload change.
2. Compare healthy and affected nodes under equivalent load.
3. Check kernel logs, device drivers, CPU frequency, scheduler, TuneD state, NUMA, network offloads, and storage behavior.
4. Compare performance counters and application percentiles.
5. Reproduce in pre-production if possible.
6. Decide whether to mitigate, roll back, or remain on the new kernel with a targeted fix.

Commands:

```bash
uname -r
rpm -q kernel-core
journalctl -k -b
journalctl --list-boots
tuned-adm active
lsblk -o NAME,SCHED
ethtool -k <interface>
```

Preserve evidence before rollback. If escalation is required, collect `sosreport` and relevant performance archives. Rollback must consider security fixes and support guidance, not only performance.

---

## Q72. The load average is high, but total CPU use is low. What is your investigation path?

**Answer:**

High load with low CPU often means tasks in uninterruptible sleep or restricted runnable work.

```bash
vmstat 1
sar -q 1
ps -eo state,pid,ppid,wchan:40,comm | awk '$1 ~ /D|R/'
cat /proc/pressure/{cpu,memory,io}
iostat -xz 1
nfsstat -m
```

Check:

- storage and NFS latency
- device or filesystem hangs
- blocked locks
- cgroup CPU quota or cpuset restrictions
- NUMA-local memory pressure
- kernel stack traces for blocked tasks

Do not add CPU until you prove CPU is the constrained resource.

---

## Q73. CPU utilization is normal, but application response time is high. What could cause this?

**Answer:**

Possible causes include:

- single-thread serialization
- lock/futex contention
- storage or network waits
- dependency latency
- CPU quota throttling
- remote NUMA memory
- direct reclaim
- DNS or TLS delay
- connection-pool exhaustion
- application queueing
- hypervisor scheduling delay

Use both on-CPU and off-CPU evidence:

```bash
top -H -p <PID>
pidstat -durwt -p <PID> 1
perf top -p <PID>
perf sched timehist
strace -ttT -f -p <PID>
ss -ti
```

The key is to decompose end-to-end response time into queue, CPU, I/O, network, lock, and dependency time.

---

## Q74. Storage utilization appears low, but application I/O is slow. Explain.

**Answer:**

Possible explanations:

- low-concurrency synchronous I/O where each request is slow
- one path or LUN degraded while aggregate device utilization is low
- latency hidden behind device-mapper layers
- cloud IOPS or throughput throttling
- filesystem/journal or `fsync()` latency
- SAN fabric errors/retries
- application waiting on metadata or locks
- one NVMe queue or CPU path overloaded

Use latency distributions and process-level attribution, not only `%util`:

```bash
iostat -xz 1
pidstat -d -p ALL 1
multipath -ll
/usr/share/bcc/tools/biolatency
/usr/share/bcc/tools/biosnoop
```

---

## Q75. A database became slower after RAM was increased. Why can that happen?

**Answer:**

Potential reasons include:

- the server now spans more NUMA nodes
- remote memory access increased
- database memory settings auto-expanded excessively
- larger dirty-page thresholds created longer flush bursts
- huge-page reservation or THP behavior changed
- memory initialization/warm-up takes longer
- firmware memory speed changed with DIMM population

Check NUMA topology, memory placement, database configuration, dirty writeback, huge pages, and memory bandwidth.

```bash
lscpu
numactl --hardware
numastat -p <db-pid>
grep -i huge /proc/meminfo
sysctl vm.dirty_ratio vm.dirty_background_ratio
```

More hardware does not guarantee better performance if topology and application configuration change.

---

## Q76. Transparent Huge Pages caused periodic database latency. How do you prove it?

**Answer:**

Look for temporal correlation between latency spikes and compaction, reclaim, or THP activity.

```bash
cat /sys/kernel/mm/transparent_hugepage/enabled
cat /sys/kernel/mm/transparent_hugepage/defrag
grep -E 'thp|compact' /proc/vmstat
cat /proc/pressure/memory
perf top
```

Compare a controlled workload using the vendor-recommended THP mode, such as `madvise` where appropriate, against the existing setting. Keep all other variables constant.

The production change should be implemented through TuneD or approved boot/runtime configuration, with rollback and proof that throughput is not harmed.

---

## Q77. A VM has persistent steal time. What is the correct escalation evidence?

**Answer:**

Collect:

- timestamped `mpstat`/`sar` showing steal
- vCPU count and topology
- guest run queue and workload rate
- guest CPU limits and pinning
- hypervisor host utilization and overcommit
- migration or maintenance events
- comparison with peer VMs

Do not attempt to solve sustained steal by changing guest swappiness or I/O scheduler. The primary issue is CPU scheduling outside the guest. Mitigations include host rebalance, reduced overcommit, resizing, pinning, or moving the VM.

---

## Q78. A container is CPU-throttled despite idle host CPUs. How do you fix it safely?

**Answer:**

1. Confirm cgroup throttling counters.
2. Check CPU quota, period, cpuset, and orchestration limits.
3. Compare requested versus actual concurrency.
4. Determine whether scaling replicas or increasing quota is appropriate.
5. Validate noisy-neighbor impact on the node.
6. Implement through version-controlled deployment configuration.

The safe fix may be a higher CPU limit, no hard limit with appropriate requests, more replicas, or application concurrency tuning. The right choice depends on platform policy and workload behavior.

---

## Q79. A tuning change improved throughput but worsened p99 latency. Is it successful?

**Answer:**

Not necessarily. Success depends on the agreed SLO and business priority.

Examples:

- larger queues can increase throughput while increasing waiting time
- larger dirty-page limits can absorb bursts but create long flush stalls
- interrupt coalescing can reduce CPU use but add latency
- larger network buffers can increase throughput but create bufferbloat
- more worker threads can increase throughput until lock and queue contention grows

The decision must use a scorecard containing throughput, p50/p95/p99 latency, errors, resource cost, recovery behavior, and stability. A change is successful only if it meets the total acceptance criteria.

---

## Q80. How do you preserve volatile performance evidence during an incident?

**Answer:**

Record:

- exact start/end timestamps and timezone
- application symptoms and SLO impact
- deployment and infrastructure changes
- process/thread state
- CPU, memory, PSI, storage, network, and cgroup snapshots
- kernel and service logs
- packet captures or traces only when justified
- host identity, kernel, configuration, and topology

A lightweight incident bundle might include:

```bash
mkdir -p /var/tmp/perf-incident
uptime > /var/tmp/perf-incident/uptime.txt
vmstat -w 1 60 > /var/tmp/perf-incident/vmstat.txt
mpstat -P ALL 1 60 > /var/tmp/perf-incident/mpstat.txt
iostat -xz 1 60 > /var/tmp/perf-incident/iostat.txt
pidstat -durwt -p ALL 1 60 > /var/tmp/perf-incident/pidstat.txt
cp /proc/pressure/* /var/tmp/perf-incident/
journalctl -k --since '-30 min' > /var/tmp/perf-incident/kernel.log
```

Ensure tools are pre-approved, commands are version tested, output is access controlled, and collection does not worsen the incident.

---

# 12. Governance, Capacity, and Senior-Level Questions

## Q81. What should a performance change record contain?

**Answer:**

A corporate change record should contain:

- business symptom and SLO impact
- technical hypothesis
- baseline evidence
- exact configuration change
- affected hosts and applications
- compatibility/supportability review
- pre-production test method
- acceptance thresholds
- rollback steps and trigger
- implementation window
- owner and peer reviewer
- post-change observation period
- links to dashboards, logs, and benchmark reports

The purpose is reproducibility and risk control, not paperwork alone.

---

## Q82. How do you perform before-and-after performance comparison?

**Answer:**

Control variables:

- same software build
- same data set and cache state
- same request profile and concurrency
- same hardware/VM placement
- same test duration and warm-up
- same background workload
- same power and TuneD profile

Report:

- throughput
- latency percentiles and histogram
- errors/timeouts
- CPU, memory, storage, and network use
- saturation and pressure
- run-to-run variation
- confidence and limitations

One successful run is weak evidence. Use multiple repetitions and report variance.

---

## Q83. How much capacity headroom should an enterprise system maintain?

**Answer:**

There is no universal percentage. Headroom depends on:

- workload variability
- scale-out speed
- failure-domain design
- batch and seasonal peaks
- infrastructure provisioning time
- SLO and recovery objectives
- resource type

A cluster may need enough capacity to survive loss of a node or availability zone while still meeting peak SLOs. CPU, memory, storage IOPS, storage capacity, and network require separate models.

The L3 answer should describe evidence-based thresholds, not quote a generic 20% or 30% rule.

---

## Q84. What is your rollback strategy for a tuning change?

**Answer:**

A rollback plan must include:

- original value/profile/configuration
- exact reversal commands
- configuration-management rollback
- restart or reboot requirement
- data-integrity considerations
- rollback trigger based on metrics
- maximum decision time
- verification after rollback

Example TuneD rollback:

```bash
tuned-adm active
tuned-adm profile <previous-profile>
tuned-adm verify
```

For kernel boot parameters, maintain a known-good boot entry and verify console or out-of-band access. Never make a high-risk change when rollback depends on the same degraded network path.

---

## Q85. When should you open a Red Hat support case for a performance issue?

**Answer:**

Escalate when evidence suggests:

- kernel or driver regression
- unexplained lockup, soft lockup, RCU stall, or hung task
- storage timeout/reset behavior
- machine-check or hardware error
- severe performance regression after supported update
- suspected memory leak in kernel space
- behavior requiring unsupported tuning to avoid impact

Provide:

- problem statement and business impact
- exact timestamps
- reproducer if available
- healthy versus affected comparison
- `sosreport`
- PCP/sysstat archives
- relevant traces and kernel logs
- recent changes and attempted mitigations

Do not send unbounded sensitive data. Follow enterprise security and support-handling procedures.

---

# 13. Rapid-Fire Command Questions

## Q86. Which commands give a fast 60-second system overview?

```bash
uptime
vmstat -w 1 60
mpstat -P ALL 1 60
pidstat -durwt -p ALL 1 60
iostat -xz 1 60
sar -n DEV,EDEV,TCP,ETCP 1 60
cat /proc/pressure/{cpu,memory,io}
```

Interpret them together. Do not declare root cause from one command.

---

## Q87. How do you identify blocked tasks?

```bash
ps -eo state,pid,ppid,wchan:40,etimes,comm | awk '$1 ~ /D/'
cat /proc/<PID>/stack
journalctl -k -g 'blocked for more than|hung task'
```

`D` state commonly represents uninterruptible sleep. Determine the wait channel and underlying subsystem.

---

## Q88. How do you find the busiest threads of a process?

```bash
top -H -p <PID>
pidstat -t -u -p <PID> 1
ps -L -p <PID> -o pid,tid,psr,pcpu,stat,comm --sort=-pcpu
```

Convert a decimal TID to hexadecimal when matching some profiler or stack outputs:

```bash
printf '%x\n' <TID>
```

---

## Q89. How do you inspect open files and file-descriptor limits?

```bash
lsof -p <PID>
ls -1 /proc/<PID>/fd | wc -l
cat /proc/<PID>/limits
prlimit --pid <PID>
systemctl show <service> -p LimitNOFILE
```

High FD use may indicate expected concurrency or a leak. Compare use with the limit and socket/file types.

---

## Q90. How do you check TCP socket state summary?

```bash
ss -s
ss -tanp
ss -ti
nstat -az
```

Look for retransmissions, queue growth, `SYN-RECV`, `TIME-WAIT`, zero-window behavior, and per-socket congestion information.

---

## Q91. How do you inspect CPU topology and NUMA layout?

```bash
lscpu -e
lscpu
numactl --hardware
numastat
```

Do not confuse sockets, cores, and hardware threads. Licensing and application scaling may depend on physical cores rather than logical CPUs.

---

## Q92. How do you check the active I/O scheduler?

```bash
lsblk -o NAME,TYPE,ROTA,SCHED
cat /sys/block/<device>/queue/scheduler
```

The scheduler shown in brackets is active. Check the correct underlying block device, not only an LVM logical name.

---

## Q93. How do you check current TuneD status?

```bash
systemctl status tuned
tuned-adm active
tuned-adm recommend
tuned-adm verify
```

A profile can be active but verification may reveal that expected values are not currently applied.

---

## Q94. How do you inspect CPU and memory pressure for a systemd service?

```bash
systemctl status <service>
systemctl show <service> -p ControlGroup
cat /sys/fs/cgroup/<resolved-path>/cpu.pressure
cat /sys/fs/cgroup/<resolved-path>/memory.pressure
cat /sys/fs/cgroup/<resolved-path>/io.pressure
```

Use the actual cgroup path and account for nested scopes created by container runtimes.

---

## Q95. How do you identify recent OOM events?

```bash
journalctl -k --since today | egrep -i 'oom|out of memory|killed process'
systemctl show <service> -p OOMPolicy
cat /sys/fs/cgroup/<path>/memory.events
```

Differentiate global OOM, service/cgroup OOM, and an application terminating itself after allocation failure.

---

# 14. Official References

The concepts and commands in this guide are aligned primarily with Red Hat's current RHEL performance documentation. Always use the documentation matching the exact RHEL major/minor release in production.

1. [RHEL 9 — Monitoring and managing system status and performance](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/index)
2. [RHEL 9 — Getting started with TuneD](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/getting-started-with-tuned_monitoring-and-managing-system-status-and-performance)
3. [RHEL 9 — Customizing TuneD profiles](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/customizing-tuned-profiles_monitoring-and-managing-system-status-and-performance)
4. [RHEL 9 — Monitoring with Performance Co-Pilot](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/monitoring-performance-with-performance-co-pilot_monitoring-and-managing-system-status-and-performance)
5. [RHEL 9 — Optimizing virtual machine performance](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/optimizing-virtual-machine-performance-in-rhel_monitoring-and-managing-system-status-and-performance)
6. [RHEL 9 — Tuning network performance](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/tuning-the-network-performance_monitoring-and-managing-system-status-and-performance)
7. [RHEL 9 — Factors affecting I/O and file-system performance](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/monitoring_and_managing_system_status_and_performance/factors-affecting-i-o-and-file-system-performance_monitoring-and-managing-system-status-and-performance)
8. [RHEL for Real Time 9 — Low-latency optimization](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_for_real_time/9/html/optimizing_rhel_9_for_real_time_for_low_latency_operation/index)

---

## Final Interview Advice

For a Corporate L3 interview, avoid answers such as "increase the buffer," "disable swap," or "change the scheduler" without evidence. A senior-quality answer should sound like this:

> I will first confirm the business symptom and affected percentile, compare it with a known-good baseline, identify utilization, saturation, errors, and pressure across each subsystem, narrow the issue to a process/thread/device/path, and then test the smallest reversible change. I will validate both application SLOs and system side effects, document rollback, and prevent configuration drift through approved automation.

That approach demonstrates production maturity, not only command knowledge.
