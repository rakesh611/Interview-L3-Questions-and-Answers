# `systemd-analyze` for Linux

## 1. What is `systemd-analyze`?

`systemd-analyze` is a command-line troubleshooting and diagnostic utility included with **systemd**.

It is primarily used to:

- Measure Linux boot time.
- Identify services that take a long time to start.
- Display the boot-critical dependency chain.
- Generate graphical boot and dependency reports.
- Validate systemd unit files before deployment.
- Review the security hardening of services.
- Test `OnCalendar=` expressions used in systemd timers.
- Inspect systemd configuration precedence and internal state.

It communicates with the systemd system or user service manager and is especially useful for Linux administrators, DevOps engineers, SREs, and performance troubleshooting teams.

> `systemd-analyze` is mainly an analysis tool. It does not automatically fix slow services or change system configuration.

---

## 2. Check availability and version

```bash
command -v systemd-analyze
systemd-analyze --version
```

Example:

```text
systemd 252
+PAM +AUDIT +SELINUX +APPARMOR ...
```

The available subcommands depend on the installed systemd version.

View built-in help:

```bash
systemd-analyze --help
```

Read the manual:

```bash
man systemd-analyze
```

---

## 3. General syntax

```bash
systemd-analyze [OPTIONS] [COMMAND] [ARGUMENTS]
```

Running the command without a subcommand is equivalent to:

```bash
systemd-analyze time
```

Most boot-analysis commands examine the current boot.

---

# Important `systemd-analyze` Commands

## 4. `systemd-analyze time`

### Purpose

Displays the time spent in:

- Linux kernel initialization.
- Initial RAM disk, or initrd.
- systemd userspace startup.
- Reaching the default target or another reported target.

### Command

```bash
systemd-analyze time
```

or:

```bash
systemd-analyze
```

### Example output

```text
Startup finished in 4.217s (kernel) + 6.884s (initrd) + 18.691s (userspace) = 29.793s
graphical.target reached after 18.642s in userspace
```

### Interpretation

- `kernel`: Time from kernel startup until initrd or userspace starts.
- `initrd`: Time spent in the initial RAM disk.
- `userspace`: Time systemd spent starting units before reaching the target.
- `total`: Kernel + initrd + userspace.

### Important limitation

The reported time indicates when systemd reached the target. It does not guarantee that:

- Every application is fully ready.
- The desktop is responsive.
- Storage activity has stopped.
- Every network-dependent service has completed its own initialization.

### Useful example

```bash
systemd-analyze time
systemctl --failed
journalctl -b -p warning
```

Use these together to check overall boot time, failed services, and warnings.

---

## 5. `systemd-analyze blame`

### Purpose

Lists loaded units in descending order of the time they remained in the activating state.

### Command

```bash
systemd-analyze blame
```

### Example output

```text
21.421s NetworkManager-wait-online.service
12.705s firewalld.service
 8.246s tuned.service
 4.912s lvm2-monitor.service
 2.841s sshd.service
```

### Show only the first 20 entries

```bash
systemd-analyze blame | head -20
```

### Search for a specific unit

```bash
systemd-analyze blame | grep -i NetworkManager
```

### Save output

```bash
systemd-analyze blame > /tmp/systemd-blame.txt
```

### How to interpret it

A large value means that a unit spent a long time activating. It does not always mean that the unit itself is defective.

Possible reasons include:

- Waiting for a network connection.
- Waiting for a block device.
- Waiting for DNS.
- Waiting for another service.
- A startup timeout.
- Slow storage.
- A dependency problem.

### Important limitation

`blame` can be misleading because systemd starts many units in parallel. A service may show a high activation time while not being on the boot-critical path.

Always confirm with:

```bash
systemd-analyze critical-chain
```

---

## 6. `systemd-analyze critical-chain`

### Purpose

Displays the chain of units that directly affected the time required to reach a target.

### Command

```bash
systemd-analyze critical-chain
```

### Example output

```text
graphical.target @18.642s
└─multi-user.target @18.640s
  └─NetworkManager-wait-online.service @7.198s +11.420s
    └─NetworkManager.service @5.630s +1.501s
      └─dbus-broker.service @5.201s +390ms
```

### Meaning of symbols

- `@18.642s`: Time after boot when the unit became active.
- `+11.420s`: Time the unit took to activate.
- Tree indentation: Dependency relationship.

### Analyze a specific target

```bash
systemd-analyze critical-chain multi-user.target
```

### Analyze a specific service

```bash
systemd-analyze critical-chain sshd.service
```

### Common use case

If `systemd-analyze blame` reports:

```text
20s NetworkManager-wait-online.service
```

Run:

```bash
systemd-analyze critical-chain NetworkManager-wait-online.service
```

Then inspect the service:

```bash
systemctl status NetworkManager-wait-online.service
journalctl -b -u NetworkManager-wait-online.service
```

---

## 7. `systemd-analyze plot`

### Purpose

Creates an SVG boot chart showing when units started and how long they took.

### Command

```bash
systemd-analyze plot > /tmp/boot-analysis.svg
```

### Open the report

On a graphical Linux system:

```bash
xdg-open /tmp/boot-analysis.svg
```

Other possible viewers:

```bash
firefox /tmp/boot-analysis.svg
google-chrome /tmp/boot-analysis.svg
```

### Verify the file

```bash
file /tmp/boot-analysis.svg
ls -lh /tmp/boot-analysis.svg
```

### Why it is useful

The chart helps visualize:

- Parallel unit startup.
- Long-running unit activation.
- Dependency timing.
- Kernel, initrd, and userspace phases.
- The exact point when a target was reached.

### Save with hostname and date

```bash
systemd-analyze plot > "/tmp/$(hostname)-boot-$(date +%F-%H%M).svg"
```

---

## 8. `systemd-analyze dot`

### Purpose

Generates a systemd dependency graph in Graphviz DOT format.

### Basic command

```bash
systemd-analyze dot > /tmp/systemd-dependencies.dot
```

The complete graph can be extremely large.

### Generate an SVG with Graphviz

Install Graphviz if necessary:

RHEL, Rocky Linux, AlmaLinux, or Fedora:

```bash
sudo dnf install graphviz
```

Debian or Ubuntu:

```bash
sudo apt install graphviz
```

Generate the graph:

```bash
systemd-analyze dot | dot -Tsvg > /tmp/systemd-dependencies.svg
```

### Graph a specific service

```bash
systemd-analyze dot 'sshd.service' | dot -Tsvg > /tmp/sshd-dependencies.svg
```

### Show ordering dependencies

```bash
systemd-analyze dot --order 'sshd.service' > /tmp/sshd-order.dot
```

### Show requirement dependencies

```bash
systemd-analyze dot --require 'sshd.service' > /tmp/sshd-require.dot
```

### Important warning

A complete dependency graph may contain thousands of relationships. Filter by unit or target whenever possible.

---

## 9. `systemd-analyze verify`

### Purpose

Checks systemd unit files for syntax errors, invalid directives, missing dependencies, and some execution problems.

This is one of the most important commands before deploying a new or modified unit.

### Validate a local unit file

```bash
systemd-analyze verify ./myapp.service
```

### Validate an installed unit

```bash
systemd-analyze verify /etc/systemd/system/myapp.service
```

### Example unit with an error

```ini
[Unit]
Description=Example Application
After=network-online.target

[Service]
Type=simple
ExecStar=/usr/local/bin/myapp

[Install]
WantedBy=multi-user.target
```

Validate it:

```bash
systemd-analyze verify ./myapp.service
```

Example error:

```text
Unknown key 'ExecStar' in section [Service], ignoring.
Service has no ExecStart= setting, which is only allowed for Type=oneshot services.
```

Correct directive:

```ini
ExecStart=/usr/local/bin/myapp
```

### Recommended deployment workflow

```bash
sudo systemd-analyze verify /etc/systemd/system/myapp.service
sudo systemctl daemon-reload
sudo systemctl start myapp.service
sudo systemctl status myapp.service
sudo journalctl -u myapp.service -b
```

### Validate multiple related units

```bash
systemd-analyze verify app.socket app.service
```

### Notes

`verify` can identify many errors before a service is restarted, but it cannot guarantee that the application itself will run correctly.

For example, it cannot fully validate:

- Application configuration files.
- Remote database availability.
- Runtime credentials.
- Business logic.
- Every possible permission problem.

---

## 10. `systemd-analyze security`

### Purpose

Reviews the sandboxing and security-hardening settings of systemd service units.

### Analyze one service

```bash
systemd-analyze security sshd.service
```

### Analyze all loaded long-running services

```bash
systemd-analyze security
```

### Disable paging

```bash
systemd-analyze security --no-pager
```

### Analyze a local unit file

Some systemd versions support offline analysis with options such as:

```bash
systemd-analyze security --offline=yes ./myapp.service
```

Check local support:

```bash
systemd-analyze security --help
```

### Common settings checked

The assessment may include:

- `User=`
- `Group=`
- `NoNewPrivileges=`
- `PrivateTmp=`
- `PrivateDevices=`
- `ProtectSystem=`
- `ProtectHome=`
- `ProtectKernelTunables=`
- `ProtectKernelModules=`
- `ProtectControlGroups=`
- `CapabilityBoundingSet=`
- `RestrictAddressFamilies=`
- `SystemCallFilter=`
- `RestrictNamespaces=`
- `LockPersonality=`
- `MemoryDenyWriteExecute=`

### Example hardening directives

```ini
[Service]
User=myapp
Group=myapp
NoNewPrivileges=yes
PrivateTmp=yes
PrivateDevices=yes
ProtectSystem=strict
ProtectHome=yes
ProtectKernelTunables=yes
ProtectKernelModules=yes
ProtectControlGroups=yes
RestrictSUIDSGID=yes
LockPersonality=yes
```

### Critical warning

Do not copy hardening options blindly into production services. Some applications legitimately need access to:

- Devices.
- Home directories.
- Writable system paths.
- Specific Linux capabilities.
- Network protocol families.
- Kernel interfaces.
- Namespace operations.

Apply one change at a time and test application functionality.

### Validation workflow

```bash
systemd-analyze security myapp.service
sudo systemctl edit myapp.service
sudo systemctl daemon-reload
sudo systemctl restart myapp.service
systemctl status myapp.service
journalctl -u myapp.service -b
systemd-analyze security myapp.service
```

---

## 11. `systemd-analyze calendar`

### Purpose

Validates and explains an `OnCalendar=` expression used in a systemd timer.

### Test a daily schedule

```bash
systemd-analyze calendar 'daily'
```

### Test a specific time

```bash
systemd-analyze calendar '*-*-* 02:30:00'
```

### Test weekdays at 09:00

```bash
systemd-analyze calendar 'Mon..Fri 09:00'
```

### Show the next five executions

```bash
systemd-analyze calendar --iterations=5 'Mon..Fri 09:00'
```

### Example output

```text
Original form: Mon..Fri 09:00
Normalized form: Mon..Fri *-*-* 09:00:00
    Next elapse: Wed 2026-07-29 09:00:00 IST
       From now: ...
```

### Validate a timer before deployment

Timer file:

```ini
[Unit]
Description=Run backup every day

[Timer]
OnCalendar=*-*-* 02:30:00
Persistent=true

[Install]
WantedBy=timers.target
```

Check the expression:

```bash
systemd-analyze calendar '*-*-* 02:30:00'
```

Then verify the unit:

```bash
systemd-analyze verify backup.timer backup.service
```

---

## 12. `systemd-analyze timestamp`

### Purpose

Parses a systemd timestamp and converts it into normalized and machine-readable forms.

### Examples

```bash
systemd-analyze timestamp now
systemd-analyze timestamp tomorrow
systemd-analyze timestamp '2026-08-01 10:30'
systemd-analyze timestamp '@1785560400'
```

### Use case

Use it when verifying timestamp formats accepted by:

- systemd timers.
- systemd configuration options.
- systemd command-line tools.

---

## 13. `systemd-analyze timespan`

### Purpose

Parses a duration and displays its normalized form and equivalent microseconds.

### Examples

```bash
systemd-analyze timespan 30s
systemd-analyze timespan 5min
systemd-analyze timespan '1h 30min'
systemd-analyze timespan 2d
```

Example:

```text
Original: 1h 30min
      μs: 5400000000
   Human: 1h 30min
```

### Use case

Useful when validating values such as:

```ini
TimeoutStartSec=2min
RestartSec=15s
RuntimeMaxSec=1h
```

---

## 14. `systemd-analyze cat-config`

### Purpose

Displays a systemd configuration file together with its drop-in files in effective precedence order.

### Show logind configuration

```bash
systemd-analyze cat-config systemd/logind.conf
```

### Show journald configuration

```bash
systemd-analyze cat-config systemd/journald.conf
```

### Show resolved configuration

```bash
systemd-analyze cat-config systemd/resolved.conf
```

### Why it is useful

A systemd configuration can be built from files in several locations, including:

```text
/usr/lib/systemd/
/usr/local/lib/systemd/
/run/systemd/
/etc/systemd/
```

Files under `/etc` normally override vendor defaults. `cat-config` helps locate the file that actually defines or overrides a value.

### Search for active settings

```bash
systemd-analyze cat-config systemd/journald.conf | grep -vE '^[[:space:]]*(#|$)'
```

---

## 15. `systemd-analyze unit-paths`

### Purpose

Lists directories searched for unit files, drop-ins, and generated units.

### Command

```bash
systemd-analyze unit-paths
```

### Show runtime-generated paths

```bash
systemd-analyze unit-paths | grep '^/run'
```

### Typical paths

```text
/etc/systemd/system
/run/systemd/system
/run/systemd/generator
/usr/local/lib/systemd/system
/usr/lib/systemd/system
```

### Use case

Use this command when:

- A unit file appears to be loaded from an unexpected location.
- A vendor unit conflicts with an administrator override.
- A generator creates a temporary unit.
- You need to understand unit-file precedence.

To see the path used by the running manager:

```bash
systemctl show -p UnitPath --value
```

---

## 16. `systemd-analyze dump`

### Purpose

Prints the internal state of the systemd manager.

### Command

```bash
sudo systemd-analyze dump
```

### Save for incident analysis

```bash
sudo systemd-analyze dump > /tmp/systemd-manager-dump.txt
```

### Dump matching units

On supported versions:

```bash
sudo systemd-analyze dump 'sshd*'
```

### Search the dump

```bash
sudo systemd-analyze dump | grep -A20 -B5 'sshd.service'
```

### Important warning

The output is long and its format is not guaranteed to remain stable. Do not build production parsers that depend on its current text format.

Use it for:

- Deep debugging.
- Support cases.
- Comparing manager state.
- Collecting evidence during incidents.

---

## 17. `systemd-analyze condition`

### Purpose

Tests `Condition...=` and `Assert...=` expressions without starting a unit.

### Test whether a path exists

```bash
systemd-analyze condition 'ConditionPathExists=/etc/os-release'
```

### Test architecture

```bash
systemd-analyze condition 'ConditionArchitecture=x86-64'
```

### Test virtualization

```bash
systemd-analyze condition 'ConditionVirtualization=vm'
```

### Test multiple conditions

```bash
systemd-analyze condition \
  'ConditionPathExists=/etc/redhat-release' \
  'ConditionArchitecture=x86-64'
```

### Use case

Suppose a unit contains:

```ini
[Unit]
ConditionPathExists=/etc/myapp/config.yml
ConditionArchitecture=x86-64
```

Test the conditions before deployment:

```bash
systemd-analyze condition \
  'ConditionPathExists=/etc/myapp/config.yml' \
  'ConditionArchitecture=x86-64'
```

---

## 18. `systemd-analyze syscall-filter`

### Purpose

Lists system calls included in systemd syscall-filter groups.

### List known groups

```bash
systemd-analyze syscall-filter
```

### Inspect a specific group

```bash
systemd-analyze syscall-filter @system-service
```

Other examples:

```bash
systemd-analyze syscall-filter @network-io
systemd-analyze syscall-filter @file-system
systemd-analyze syscall-filter @privileged
```

### Use with a unit

```ini
[Service]
SystemCallFilter=@system-service
```

### Warning

System-call filtering can break applications in ways that appear as permission or runtime errors. Test under realistic workload before production rollout.

---

## 19. `systemd-analyze capability`

### Purpose

Lists Linux capabilities and converts capability numbers or masks into names.

### List capabilities

```bash
systemd-analyze capability
```

### Look up specific capabilities

```bash
systemd-analyze capability CAP_NET_BIND_SERVICE CAP_SYS_ADMIN
```

### Decode a hexadecimal capability mask

```bash
systemd-analyze capability --mask 0000000000003c00
```

### Read process capability masks

```bash
grep '^Cap' /proc/$(pidof sshd | awk '{print $1}')/status
```

Then decode one of the masks:

```bash
systemd-analyze capability --mask <HEX_MASK>
```

### Use case

Helps design directives such as:

```ini
CapabilityBoundingSet=CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_BIND_SERVICE
```

---

## 20. `systemd-analyze exit-status`

### Purpose

Maps numeric process exit codes to their symbolic names and classes.

### List all known exit statuses

```bash
systemd-analyze exit-status
```

### Check selected codes

```bash
systemd-analyze exit-status 0 1 2 64 65 203
```

Example:

```text
NAME       STATUS CLASS
SUCCESS         0 libc
FAILURE         1 libc
USAGE          64 BSD
EXEC           203 systemd
```

### Useful troubleshooting example

A service fails with:

```text
status=203/EXEC
```

Check:

```bash
systemd-analyze exit-status 203
```

Then inspect:

```bash
systemctl status myapp.service
systemctl cat myapp.service
ls -l /path/from/ExecStart
file /path/from/ExecStart
```

Common causes of `203/EXEC` include:

- Incorrect `ExecStart=` path.
- Missing executable.
- No execute permission.
- Invalid script interpreter.
- SELinux denial.
- Incompatible executable format.

---

## 21. `systemd-analyze compare-versions`

### Purpose

Compares version strings using systemd version ordering.

### Compare two versions

```bash
systemd-analyze compare-versions 252 lt 255
echo $?
```

### Test equality or ordering

```bash
systemd-analyze compare-versions 9.4 ge 9.2
echo $?
```

### Operators

Common operators include:

```text
lt  <
le  <=
eq  ==
ne  !=
ge  >=
gt  >
```

### Shell example

```bash
if systemd-analyze compare-versions "$(systemd-analyze --version | awk 'NR==1 {print $2}')" ge 252; then
    echo "Required systemd version is available"
else
    echo "Installed systemd version is too old"
fi
```

Check `systemd-analyze compare-versions --help` on the local system because availability depends on the systemd version.

---

## 22. `systemd-analyze architectures`

### Purpose

Lists architecture names understood by systemd and shows which architecture is native.

### Command

```bash
systemd-analyze architectures
```

### Check one architecture

```bash
systemd-analyze architectures x86-64
```

### Use case

Useful when working with:

```ini
ConditionArchitecture=x86-64
```

---

# Important Options

## 23. Analyze the user service manager

Many commands inspect the system manager by default. Use `--user` for the current user's systemd manager.

```bash
systemd-analyze --user time
systemd-analyze --user blame
systemd-analyze --user critical-chain
systemd-analyze --user dump
systemd-analyze --user unit-paths
```

List user services:

```bash
systemctl --user list-units --type=service
```

---

## 24. Disable the pager

```bash
systemd-analyze --no-pager blame
systemd-analyze --no-pager security sshd.service
```

This is useful in scripts, SSH sessions, and redirected output.

---

## 25. Produce JSON output where supported

Some subcommands and systemd versions support JSON output.

Check support:

```bash
systemd-analyze --help
systemd-analyze plot --help
```

Possible example on supported releases:

```bash
systemd-analyze plot --json=pretty
```

Do not assume that every distribution version supports the same output formats.

---

# Practical Boot Troubleshooting Workflow

## 26. Step 1: Measure total boot time

```bash
systemd-analyze time
```

Determine whether the delay is mainly in:

- Kernel.
- initrd.
- Userspace.

If the delay is in the kernel or initrd, also inspect:

```bash
journalctl -b -k
journalctl -b -u systemd-udev-settle.service
lsblk -f
cat /proc/cmdline
```

---

## 27. Step 2: Find slow units

```bash
systemd-analyze blame | head -20
```

Do not disable a unit only because it appears at the top.

---

## 28. Step 3: Confirm the critical path

```bash
systemd-analyze critical-chain
```

For a specific unit:

```bash
systemd-analyze critical-chain NetworkManager-wait-online.service
```

---

## 29. Step 4: Inspect service status and logs

```bash
systemctl status <unit>
journalctl -b -u <unit>
```

Example:

```bash
systemctl status NetworkManager-wait-online.service
journalctl -b -u NetworkManager-wait-online.service
```

---

## 30. Step 5: Check failed units

```bash
systemctl --failed
```

Inspect each failure:

```bash
systemctl status <failed-unit>
journalctl -b -u <failed-unit>
```

---

## 31. Step 6: Review boot warnings and errors

```bash
journalctl -b -p warning
```

Only errors:

```bash
journalctl -b -p err
```

Kernel messages:

```bash
journalctl -b -k
```

---

## 32. Step 7: Generate visual evidence

```bash
systemd-analyze plot > /tmp/boot.svg
systemd-analyze dot 'problem-unit.service' | dot -Tsvg > /tmp/problem-unit.svg
```

---

## 33. Step 8: Validate changes

Before restarting a modified service:

```bash
systemd-analyze verify /etc/systemd/system/problem-unit.service
```

After making a unit-file change:

```bash
sudo systemctl daemon-reload
sudo systemctl restart problem-unit.service
systemctl status problem-unit.service
journalctl -b -u problem-unit.service
```

Measure again after the next reboot:

```bash
systemd-analyze time
systemd-analyze blame | head -20
systemd-analyze critical-chain
```

---

# Production Example: Slow Network Wait During Boot

## 34. Symptom

The server takes approximately 90 seconds to reach `multi-user.target`.

### Initial checks

```bash
systemd-analyze time
systemd-analyze blame | head
systemd-analyze critical-chain
```

Example finding:

```text
1min 30.012s NetworkManager-wait-online.service
```

### Inspect the service

```bash
systemctl status NetworkManager-wait-online.service
journalctl -b -u NetworkManager-wait-online.service
nmcli device status
nmcli connection show
```

### Possible root causes

- A disconnected interface is configured to autoconnect.
- A connection profile waits for DHCP.
- An incorrect gateway or VLAN configuration exists.
- A bond or team interface is incomplete.
- A service has an unnecessary dependency on `network-online.target`.

### Dependency investigation

```bash
systemctl list-dependencies --reverse network-online.target
systemctl show <application.service> -p After -p Wants -p Requires
systemctl cat <application.service>
```

### Safe approach

Do not immediately disable the wait-online service. First identify which application requires a fully configured network.

After correcting the root cause:

```bash
sudo reboot
systemd-analyze time
systemd-analyze critical-chain
```

---

# Production Example: Validate a Custom Service

## 35. Create a service

```bash
sudo vi /etc/systemd/system/report-generator.service
```

```ini
[Unit]
Description=Generate daily operations report
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
User=report
Group=report
ExecStart=/usr/local/bin/generate-report.sh
NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=strict
ProtectHome=yes
ReadWritePaths=/var/lib/report-generator

[Install]
WantedBy=multi-user.target
```

### Verify syntax and references

```bash
sudo systemd-analyze verify /etc/systemd/system/report-generator.service
```

### Review security

```bash
sudo systemd-analyze security report-generator.service
```

If the unit has not yet been loaded, local offline security analysis may be available depending on the installed systemd version.

### Reload and test

```bash
sudo systemctl daemon-reload
sudo systemctl start report-generator.service
sudo systemctl status report-generator.service
sudo journalctl -u report-generator.service -b
```

---

# Common Mistakes

## 36. Treating `blame` as the final root cause

Wrong approach:

```text
The first unit in systemd-analyze blame must be the reason for slow boot.
```

Correct approach:

```bash
systemd-analyze blame
systemd-analyze critical-chain
systemctl status <unit>
journalctl -b -u <unit>
```

---

## 37. Disabling services without dependency analysis

Before disabling a service, check:

```bash
systemctl list-dependencies --reverse <unit>
systemctl show <unit> -p WantedBy -p RequiredBy -p Before -p After
systemctl cat <unit>
```

A service that appears unnecessary may be required by another workload.

---

## 38. Editing vendor unit files directly

Avoid editing files under:

```text
/usr/lib/systemd/system/
```

Package upgrades may overwrite them.

Use a drop-in:

```bash
sudo systemctl edit myapp.service
```

View the effective unit:

```bash
systemctl cat myapp.service
```

Then validate and reload:

```bash
systemd-analyze verify /etc/systemd/system/myapp.service.d/override.conf
sudo systemctl daemon-reload
```

Note that validating an isolated drop-in file may not provide the same context as validating the complete unit. Validate the main unit path when possible.

---

## 39. Forgetting `daemon-reload`

After changing a unit file:

```bash
sudo systemctl daemon-reload
```

Then restart or start the affected service:

```bash
sudo systemctl restart myapp.service
```

---

## 40. Ignoring version differences

Check:

```bash
systemd-analyze --version
systemd-analyze --help
```

Commands available on a new Fedora release may not exist on an older RHEL, CentOS, Debian, or Ubuntu release.

---

# Quick Command Cheat Sheet

```bash
# Show total boot time
systemd-analyze time

# Same as "time"
systemd-analyze

# List units by activation time
systemd-analyze blame

# Show top 20 slow units
systemd-analyze blame | head -20

# Show boot-critical dependency chain
systemd-analyze critical-chain

# Show critical chain for a unit
systemd-analyze critical-chain sshd.service

# Create an SVG boot chart
systemd-analyze plot > /tmp/boot.svg

# Create a dependency graph
systemd-analyze dot 'sshd.service' | dot -Tsvg > /tmp/sshd.svg

# Validate a service file
systemd-analyze verify ./myapp.service

# Assess service security
systemd-analyze security myapp.service

# Validate an OnCalendar expression
systemd-analyze calendar --iterations=5 'Mon..Fri 09:00'

# Parse a timestamp
systemd-analyze timestamp tomorrow

# Parse a duration
systemd-analyze timespan '1h 30min'

# Show effective systemd configuration and drop-ins
systemd-analyze cat-config systemd/journald.conf

# Show unit search paths
systemd-analyze unit-paths

# Dump manager state
sudo systemd-analyze dump > /tmp/systemd-dump.txt

# Test a systemd condition
systemd-analyze condition 'ConditionPathExists=/etc/os-release'

# List a system-call group
systemd-analyze syscall-filter @system-service

# List Linux capabilities
systemd-analyze capability

# Decode a capability mask
systemd-analyze capability --mask 0000000000003c00

# Decode a systemd exit status
systemd-analyze exit-status 203

# Compare version strings
systemd-analyze compare-versions 252 lt 255

# Analyze the user manager
systemd-analyze --user blame

# Disable pager
systemd-analyze --no-pager security sshd.service
```

---

# Recommended L3 Incident Collection

Use this command set to collect boot-related evidence:

```bash
mkdir -p /tmp/systemd-boot-analysis

systemd-analyze time \
  > /tmp/systemd-boot-analysis/time.txt

systemd-analyze blame \
  > /tmp/systemd-boot-analysis/blame.txt

systemd-analyze critical-chain \
  > /tmp/systemd-boot-analysis/critical-chain.txt

systemd-analyze plot \
  > /tmp/systemd-boot-analysis/boot.svg

sudo systemd-analyze dump \
  > /tmp/systemd-boot-analysis/systemd-dump.txt

systemctl --failed \
  > /tmp/systemd-boot-analysis/failed-units.txt

journalctl -b -p warning \
  > /tmp/systemd-boot-analysis/boot-warnings.txt

journalctl -b -k \
  > /tmp/systemd-boot-analysis/kernel-boot.txt

tar -czf /tmp/systemd-boot-analysis.tar.gz \
  -C /tmp systemd-boot-analysis
```

Review the archive:

```bash
tar -tzf /tmp/systemd-boot-analysis.tar.gz
```

---

# Summary

The most important commands for day-to-day Linux administration are:

1. `systemd-analyze time` — Measure total boot duration.
2. `systemd-analyze blame` — Find units with long activation times.
3. `systemd-analyze critical-chain` — Identify the boot-critical dependency path.
4. `systemd-analyze plot` — Create a visual boot timeline.
5. `systemd-analyze verify` — Validate unit files before deployment.
6. `systemd-analyze security` — Review service sandboxing and hardening.
7. `systemd-analyze calendar` — Validate systemd timer schedules.
8. `systemd-analyze cat-config` — Display effective configuration and drop-ins.
9. `systemd-analyze dump` — Collect deep systemd manager state.
10. `systemd-analyze condition` — Test unit conditions and assertions.

For boot-performance troubleshooting, use this order:

```text
time → blame → critical-chain → status/logs → plot → verify → retest
```

---

## Reference commands

```bash
man systemd-analyze
man systemd.unit
man systemd.service
man systemd.timer
man systemd.time
man systemd.exec
```
