# `systemctl` for Linux

## 1. What is `systemctl`?

`systemctl` is the primary command-line utility used to control and inspect the **systemd system and service manager** on modern Linux distributions.

It is used to manage:

- Services
- Targets
- Timers
- Sockets
- Mount and automount units
- Swap units
- Device units
- Paths
- Slices and scopes
- User-level services
- Reboot, shutdown, suspend, and other system states

Simple relationship:

```text
systemd  = Linux service manager and init system
systemctl = command used to control systemd
```

Example:

```bash
systemctl status sshd
```

This command asks systemd for the current status of the SSH service.

---

## 2. General Syntax

```bash
systemctl [OPTIONS] COMMAND [UNIT]
```

Examples:

```bash
systemctl status sshd
sudo systemctl start firewalld
sudo systemctl enable chronyd
sudo systemctl restart NetworkManager
```

A complete unit name includes a suffix:

```text
sshd.service
firewalld.service
multi-user.target
backup.timer
httpd.socket
data.mount
```

For service units, `.service` is normally optional:

```bash
systemctl status sshd
systemctl status sshd.service
```

---

# Important Service Management Commands

## 3. Check Service Status

```bash
systemctl status <service>
```

Example:

```bash
systemctl status sshd
```

Typical output:

```text
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-07-28 09:10:21 IST
   Main PID: 1024 (sshd)
      Tasks: 1
     Memory: 8.5M
        CPU: 1.024s
     CGroup: /system.slice/sshd.service
             └─1024 "sshd: /usr/sbin/sshd -D"
```

Important fields:

- `Loaded`: Whether the unit file was found.
- `enabled`: Whether it is configured for automatic startup.
- `Active`: Current runtime state.
- `Main PID`: Main service process.
- `Memory`: Current memory use.
- `CPU`: CPU time consumed.
- `CGroup`: Control group containing service processes.

Show complete lines without paging:

```bash
systemctl --no-pager -l status sshd
```

---

## 4. Start a Service

```bash
sudo systemctl start <service>
```

Example:

```bash
sudo systemctl start httpd
```

Verify:

```bash
systemctl status httpd
systemctl is-active httpd
```

`start` starts the service immediately, but does not automatically enable it at boot.

---

## 5. Stop a Service

```bash
sudo systemctl stop <service>
```

Example:

```bash
sudo systemctl stop httpd
```

Verify:

```bash
systemctl is-active httpd
```

Stopping a service does not disable its automatic startup.

---

## 6. Restart a Service

```bash
sudo systemctl restart <service>
```

Example:

```bash
sudo systemctl restart sshd
```

A restart stops and starts the service. It may interrupt active connections.

Before restarting a production service:

```bash
systemctl status <service>
systemctl cat <service>
journalctl -u <service> -n 50
```

For SSH changes, validate first:

```bash
sudo sshd -t
sudo systemctl reload sshd
```

Keep the current SSH session open while testing from another terminal.

---

## 7. Reload a Service

```bash
sudo systemctl reload <service>
```

Example:

```bash
sudo systemctl reload httpd
```

Reload asks the application to reread its configuration without a full restart.

Check whether reload is supported:

```bash
systemctl show httpd -p CanReload
```

Possible output:

```text
CanReload=yes
```

Not every service supports reload.

---

## 8. Reload or Restart

```bash
sudo systemctl reload-or-restart <service>
```

Example:

```bash
sudo systemctl reload-or-restart httpd
```

Behavior:

- Reloads when reload is supported.
- Otherwise restarts the service.

This is useful in automation.

---

## 9. Restart Only If Already Running

```bash
sudo systemctl try-restart <service>
```

Example:

```bash
sudo systemctl try-restart rsyslog
```

It restarts the unit only when it is currently active.

---

# Service Boot Enablement

## 10. Enable a Service at Boot

```bash
sudo systemctl enable <service>
```

Example:

```bash
sudo systemctl enable sshd
```

Verify:

```bash
systemctl is-enabled sshd
```

`enable` configures automatic startup but does not necessarily start the service immediately.

---

## 11. Enable and Start Together

```bash
sudo systemctl enable --now <service>
```

Example:

```bash
sudo systemctl enable --now chronyd
```

Verify:

```bash
systemctl is-enabled chronyd
systemctl is-active chronyd
```

---

## 12. Disable a Service at Boot

```bash
sudo systemctl disable <service>
```

Example:

```bash
sudo systemctl disable httpd
```

This does not stop a currently running service.

Disable and stop together:

```bash
sudo systemctl disable --now httpd
```

---

## 13. Check Whether a Unit Is Enabled

```bash
systemctl is-enabled <service>
```

Example:

```bash
systemctl is-enabled firewalld
```

Common results:

```text
enabled
disabled
static
masked
indirect
generated
transient
```

Meaning:

- `enabled`: Starts automatically through installed links.
- `disabled`: Not configured for automatic startup.
- `static`: Cannot normally be enabled directly; started by dependencies.
- `masked`: Completely blocked from starting.
- `generated`: Created dynamically by a systemd generator.
- `transient`: Created temporarily at runtime.

---

## 14. Check Whether a Service Is Active

```bash
systemctl is-active <service>
```

Example:

```bash
systemctl is-active sshd
```

Possible states:

```text
active
inactive
failed
activating
deactivating
```

Script example:

```bash
if systemctl is-active --quiet sshd; then
    echo "sshd is running"
else
    echo "sshd is not running"
fi
```

---

## 15. Check Whether a Service Failed

```bash
systemctl is-failed <service>
```

Example:

```bash
systemctl is-failed httpd
```

Script example:

```bash
if systemctl is-failed --quiet httpd; then
    echo "httpd is in failed state"
fi
```

---

# Listing Units and Unit Files

## 16. List Running Services

```bash
systemctl list-units --type=service --state=running
```

---

## 17. List All Loaded Services

```bash
systemctl list-units --type=service
```

Include inactive services:

```bash
systemctl list-units --type=service --all
```

---

## 18. List Installed Service Unit Files

```bash
systemctl list-unit-files --type=service
```

Enabled service files:

```bash
systemctl list-unit-files --type=service --state=enabled
```

Disabled service files:

```bash
systemctl list-unit-files --type=service --state=disabled
```

Difference:

```text
list-units      = units currently loaded in memory
list-unit-files = unit files installed on disk
```

---

## 19. List Failed Units

```bash
systemctl --failed
```

Only services:

```bash
systemctl --failed --type=service
```

No pager:

```bash
systemctl --failed --no-pager
```

Troubleshooting workflow:

```bash
systemctl --failed
systemctl status <failed-unit>
journalctl -b -u <failed-unit>
```

---

## 20. Reset Failed State

```bash
sudo systemctl reset-failed <service>
```

Example:

```bash
sudo systemctl reset-failed httpd
```

Reset all failed states:

```bash
sudo systemctl reset-failed
```

After correcting the root cause:

```bash
sudo systemctl reset-failed httpd
sudo systemctl start httpd
```

---

# Inspecting Unit Files and Properties

## 21. Show a Unit File and Its Drop-ins

```bash
systemctl cat <unit>
```

Example:

```bash
systemctl cat sshd.service
```

This displays:

- Main unit file
- Administrator drop-ins
- Unit file paths
- Effective content loaded from disk

---

## 22. Show the Unit File Path

```bash
systemctl show <unit> -p FragmentPath
```

Example:

```bash
systemctl show sshd.service -p FragmentPath
```

Show drop-in paths:

```bash
systemctl show sshd.service -p DropInPaths
```

---

## 23. Show Unit Properties

```bash
systemctl show <unit>
```

Examples:

```bash
systemctl show sshd -p ActiveState
systemctl show sshd -p SubState
systemctl show sshd -p MainPID
systemctl show sshd -p ExecMainStatus
systemctl show sshd -p MemoryCurrent
systemctl show sshd -p CPUUsageNSec
systemctl show sshd -p Restart
systemctl show sshd -p TimeoutStartUSec
```

Show several properties:

```bash
systemctl show sshd \
  -p ActiveState \
  -p SubState \
  -p MainPID \
  -p ExecMainStatus
```

Show only the value:

```bash
systemctl show sshd -p MainPID --value
```

---

## 24. Edit a Service Safely with a Drop-in

```bash
sudo systemctl edit <service>
```

Example:

```bash
sudo systemctl edit sshd
```

Example override:

```ini
[Service]
Restart=on-failure
RestartSec=5s
```

Apply:

```bash
sudo systemctl daemon-reload
sudo systemctl restart sshd
```

Review:

```bash
systemctl cat sshd
```

Do not directly edit vendor files under `/usr/lib/systemd/system/` unless there is a specific reason.

---

## 25. Edit the Full Unit

```bash
sudo systemctl edit --full <service>
```

Example:

```bash
sudo systemctl edit --full myapp.service
```

Prefer a drop-in for small changes.

---

## 26. Revert Local Overrides

```bash
sudo systemctl revert <service>
```

Example:

```bash
sudo systemctl revert sshd
```

Review before reverting:

```bash
systemctl cat sshd
```

---

## 27. Reload systemd Unit Definitions

```bash
sudo systemctl daemon-reload
```

Use after:

- Creating a unit file
- Editing a unit file manually
- Adding a drop-in
- Changing dependencies
- Changing timer, socket, mount, or service units

Typical workflow:

```bash
sudo vi /etc/systemd/system/myapp.service
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
```

---

## 28. Reexecute the systemd Manager

```bash
sudo systemctl daemon-reexec
```

Difference:

```text
daemon-reload = reread unit definitions
daemon-reexec = reexecute the systemd manager process
```

Use `daemon-reexec` mainly after systemd upgrades or for advanced troubleshooting.

---

# Masking and Unmasking

## 29. Mask a Service

```bash
sudo systemctl mask <service>
```

Example:

```bash
sudo systemctl mask cups.service
```

A masked unit cannot be started manually or automatically.

Mask and stop:

```bash
sudo systemctl mask --now cups.service
```

Check reverse dependencies first:

```bash
systemctl list-dependencies --reverse cups.service
```

---

## 30. Unmask a Service

```bash
sudo systemctl unmask <service>
```

Example:

```bash
sudo systemctl unmask cups.service
sudo systemctl enable --now cups.service
```

---

# Dependency Analysis

## 31. Show Dependencies

```bash
systemctl list-dependencies <unit>
```

Example:

```bash
systemctl list-dependencies sshd.service
```

Show all recursively:

```bash
systemctl list-dependencies --all sshd.service
```

---

## 32. Show Reverse Dependencies

```bash
systemctl list-dependencies --reverse <unit>
```

Example:

```bash
systemctl list-dependencies --reverse network-online.target
```

Use this before disabling, masking, or removing a service.

---

## 33. Show Ordering and Requirement Relationships

```bash
systemctl show <unit> -p After
systemctl show <unit> -p Before
systemctl show <unit> -p Wants
systemctl show <unit> -p Requires
systemctl show <unit> -p WantedBy
systemctl show <unit> -p RequiredBy
```

Example:

```bash
systemctl show sshd.service \
  -p After \
  -p Before \
  -p Wants \
  -p Requires
```

Important distinction:

- `After=` and `Before=` control ordering.
- `Wants=` and `Requires=` control dependency requirements.

---

# Targets and Boot Modes

## 34. What Is a Target?

A target groups multiple units and represents a system state.

Common targets:

```text
poweroff.target
rescue.target
multi-user.target
graphical.target
reboot.target
emergency.target
```

Legacy runlevel mapping:

```text
runlevel0.target -> poweroff.target
runlevel1.target -> rescue.target
runlevel2.target -> multi-user.target
runlevel3.target -> multi-user.target
runlevel4.target -> multi-user.target
runlevel5.target -> graphical.target
runlevel6.target -> reboot.target
```

---

## 35. Check the Default Boot Target

```bash
systemctl get-default
```

Example:

```text
graphical.target
```

---

## 36. Set the Default Boot Target

Text mode:

```bash
sudo systemctl set-default multi-user.target
```

Graphical mode:

```bash
sudo systemctl set-default graphical.target
```

Verify:

```bash
systemctl get-default
```

---

## 37. Switch Target Immediately

```bash
sudo systemctl isolate <target>
```

Examples:

```bash
sudo systemctl isolate multi-user.target
sudo systemctl isolate graphical.target
```

Warning: `isolate` stops units not required by the selected target. Use carefully on remote production systems.

---

## 38. Enter Rescue Mode

```bash
sudo systemctl rescue
```

or:

```bash
sudo systemctl isolate rescue.target
```

---

## 39. Enter Emergency Mode

```bash
sudo systemctl emergency
```

or:

```bash
sudo systemctl isolate emergency.target
```

Emergency mode is more minimal than rescue mode.

---

# Power Management Commands

## 40. Reboot

```bash
sudo systemctl reboot
```

Force reboot:

```bash
sudo systemctl reboot --force
```

Using multiple `--force` options may bypass normal shutdown and risk data loss.

---

## 41. Power Off

```bash
sudo systemctl poweroff
```

---

## 42. Halt

```bash
sudo systemctl halt
```

`halt` stops the system but may not power off the machine on every platform.

---

## 43. Suspend

```bash
sudo systemctl suspend
```

---

## 44. Hibernate

```bash
sudo systemctl hibernate
```

Hibernate requires properly configured swap and hardware support.

---

## 45. Hybrid Sleep

```bash
sudo systemctl hybrid-sleep
```

---

# Timer Units

## 46. List Timers

```bash
systemctl list-timers
```

Show all timers:

```bash
systemctl list-timers --all
```

Important columns:

- `NEXT`: Next execution time
- `LEFT`: Time remaining
- `LAST`: Previous execution
- `PASSED`: Time since previous execution
- `UNIT`: Timer unit
- `ACTIVATES`: Service activated by the timer

---

## 47. Check a Timer

```bash
systemctl status backup.timer
systemctl status backup.service
```

Logs:

```bash
journalctl -u backup.timer
journalctl -u backup.service
```

---

## 48. Enable and Start a Timer

```bash
sudo systemctl enable --now backup.timer
```

Verify:

```bash
systemctl list-timers --all | grep backup
```

Test the related service manually:

```bash
sudo systemctl start backup.service
```

---

# Socket Units

## 49. List Socket Units

```bash
systemctl list-units --type=socket
systemctl list-unit-files --type=socket
```

List active listeners:

```bash
systemctl list-sockets
```

Show all:

```bash
systemctl list-sockets --all
```

---

# Mount Units

## 50. List Mount Units

```bash
systemctl list-units --type=mount
```

Include inactive:

```bash
systemctl list-units --type=mount --all
```

Check a mount unit:

```bash
systemctl status mnt-data.mount
```

Convert a path to a mount unit name:

```bash
systemd-escape --path --suffix=mount /mnt/data
```

Output:

```text
mnt-data.mount
```

---

# User Services

## 51. Manage User-Level Services

Use `--user`:

```bash
systemctl --user status
systemctl --user list-units
systemctl --user start myapp.service
systemctl --user enable myapp.service
systemctl --user enable --now myapp.service
```

User units are normally stored under:

```text
~/.config/systemd/user/
/etc/systemd/user/
/usr/lib/systemd/user/
```

After changing a user unit:

```bash
systemctl --user daemon-reload
```

---

## 52. Keep User Services Running After Logout

```bash
sudo loginctl enable-linger <username>
```

Example:

```bash
sudo loginctl enable-linger rakesh
```

Check:

```bash
loginctl show-user rakesh -p Linger
```

Disable:

```bash
sudo loginctl disable-linger rakesh
```

---

# Process and Signal Management

## 53. Kill Processes in a Unit

```bash
sudo systemctl kill <service>
```

Example:

```bash
sudo systemctl kill httpd.service
```

Send a specific signal:

```bash
sudo systemctl kill --signal=SIGTERM httpd.service
```

Kill all processes in the unit:

```bash
sudo systemctl kill --kill-who=all httpd.service
```

Possible `--kill-who` values include:

```text
main
control
all
```

---

## 54. Show the Main PID

```bash
systemctl show <service> -p MainPID --value
```

Example:

```bash
systemctl show sshd -p MainPID --value
```

---

# Common Troubleshooting Scenarios

## 55. Service Does Not Start

Run:

```bash
sudo systemctl start <service>
systemctl --no-pager -l status <service>
journalctl -b -u <service> --no-pager
```

Inspect properties:

```bash
systemctl show <service> -p Result -p ExecMainStatus
systemctl cat <service>
```

Common causes:

- Incorrect executable path
- Invalid application configuration
- Permission problem
- SELinux denial
- Missing dependency
- Port already in use
- Missing directory
- Invalid service user or group
- Read-only filesystem
- Resource limit exceeded

---

## 56. Service Starts Manually but Not at Boot

Check:

```bash
systemctl is-enabled <service>
systemctl status <service>
journalctl -b -u <service>
```

Enable:

```bash
sudo systemctl enable <service>
```

Inspect target relationship:

```bash
systemctl show <service> -p WantedBy -p RequiredBy
```

---

## 57. Service Restarts Repeatedly

Check:

```bash
systemctl status <service>
systemctl show <service> -p Restart -p RestartUSec -p NRestarts
journalctl -b -u <service>
```

Possible causes:

- `Restart=always`
- Application exits immediately
- Invalid configuration
- Missing dependency
- Port conflict
- Permission problem
- Resource exhaustion

---

## 58. Start Request Repeated Too Quickly

Typical error:

```text
Start request repeated too quickly
```

Inspect:

```bash
systemctl status <service>
systemctl show <service> \
  -p StartLimitIntervalUSec \
  -p StartLimitBurst \
  -p NRestarts
```

After fixing the root cause:

```bash
sudo systemctl reset-failed <service>
sudo systemctl start <service>
```

---

## 59. Unit Is Masked

Error:

```text
Unit xyz.service is masked
```

Check:

```bash
systemctl is-enabled xyz.service
```

Fix:

```bash
sudo systemctl unmask xyz.service
sudo systemctl enable --now xyz.service
```

---

## 60. Unit Not Found

Error:

```text
Unit xyz.service could not be found
```

Check:

```bash
systemctl list-unit-files | grep -i xyz
find /etc/systemd/system /usr/lib/systemd/system -iname '*xyz*'
```

If the unit was just created:

```bash
sudo systemctl daemon-reload
```

On RHEL-like systems, find the package owner:

```bash
rpm -qf /usr/lib/systemd/system/xyz.service
```

On Debian or Ubuntu:

```bash
dpkg -S /lib/systemd/system/xyz.service
```

---

## 61. Exit Status 203/EXEC

Check:

```bash
systemctl status <service>
systemctl cat <service>
systemctl show <service> -p ExecStart
```

Verify the executable:

```bash
ls -l /path/from/ExecStart
file /path/from/ExecStart
head -1 /path/from/ExecStart
```

Common causes:

- Wrong `ExecStart=` path
- File is not executable
- Invalid script shebang
- Missing interpreter
- SELinux denial
- Incompatible binary format

---

## 62. Check SELinux Denials

On RHEL-like systems:

```bash
sudo ausearch -m AVC,USER_AVC -ts recent
```

Detailed analysis:

```bash
sudo sealert -a /var/log/audit/audit.log
```

Check contexts:

```bash
ps -eZ | grep <service>
ls -lZ /path/to/file
```

Do not permanently disable SELinux as a troubleshooting shortcut.

---

# Creating a Custom Service

## 63. Example Service Unit

Create the unit:

```bash
sudo vi /etc/systemd/system/myapp.service
```

Content:

```ini
[Unit]
Description=My Custom Application
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=myapp
Group=myapp
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/myapp
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

Create the account and directory:

```bash
sudo useradd --system --home-dir /opt/myapp --shell /sbin/nologin myapp
sudo mkdir -p /opt/myapp/bin
sudo chown -R myapp:myapp /opt/myapp
```

Validate:

```bash
systemd-analyze verify /etc/systemd/system/myapp.service
```

Reload and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
```

Check:

```bash
systemctl status myapp.service
journalctl -u myapp.service -f
```

---

# Important Options

## 64. `--no-pager`

```bash
systemctl --no-pager status sshd
```

Useful in scripts and remote sessions.

---

## 65. `-l` or `--full`

```bash
systemctl -l status sshd
```

Shows complete lines without truncation.

---

## 66. `--quiet`

```bash
systemctl is-active --quiet sshd
echo $?
```

Exit status `0` means the condition is true.

---

## 67. `--now`

```bash
sudo systemctl enable --now sshd
sudo systemctl disable --now httpd
```

Combines enable or disable with immediate runtime action.

---

## 68. `--runtime`

Creates temporary enablement or masking changes that disappear after reboot.

Example:

```bash
sudo systemctl mask --runtime myapp.service
```

---

## 69. `--user`

```bash
systemctl --user status myapp.service
```

Controls the current user's systemd manager.

---

# Important Differences

## 70. `start` vs `enable`

```text
start  = run the service now
enable = start the service automatically at boot
```

Combined:

```bash
sudo systemctl enable --now sshd
```

---

## 71. `stop` vs `disable`

```text
stop    = stop the service now
disable = prevent automatic startup
```

Combined:

```bash
sudo systemctl disable --now httpd
```

---

## 72. `restart` vs `reload`

```text
restart = stop and start the process
reload  = reread configuration without full restart
```

Check reload support:

```bash
systemctl show <service> -p CanReload
```

---

## 73. `disable` vs `mask`

```text
disable = prevents normal automatic startup
mask    = completely prevents the unit from starting
```

---

## 74. `daemon-reload` vs Service Reload

```bash
sudo systemctl daemon-reload
```

Reloads systemd unit definitions.

```bash
sudo systemctl reload <service>
```

Reloads the application's configuration.

They are different operations.

---

# Production-Safe Troubleshooting Workflow

## 75. Step 1: Check Status

```bash
systemctl --no-pager -l status <service>
```

## 76. Step 2: Check Runtime and Enablement

```bash
systemctl is-active <service>
systemctl is-enabled <service>
systemctl is-failed <service>
```

## 77. Step 3: Review Logs

```bash
journalctl -b -u <service> --no-pager
```

Recent logs:

```bash
journalctl -u <service> --since "30 minutes ago"
```

## 78. Step 4: Inspect Effective Unit Configuration

```bash
systemctl cat <service>
systemctl show <service> -p FragmentPath -p DropInPaths
```

## 79. Step 5: Validate Application Configuration

Examples:

```bash
sshd -t
nginx -t
apachectl configtest
named-checkconf
haproxy -c -f /etc/haproxy/haproxy.cfg
```

## 80. Step 6: Check Dependencies

```bash
systemctl list-dependencies <service>
systemctl list-dependencies --reverse <service>
```

## 81. Step 7: Apply the Safest Action

Preferred order:

```text
reload
reload-or-restart
restart
stop and start
```

## 82. Step 8: Verify

```bash
systemctl status <service>
systemctl is-active <service>
journalctl -u <service> -n 50
```

---

# L3 Incident Collection Script

## 83. Collect Service Evidence

```bash
#!/bin/bash

SERVICE="${1:-sshd}"
OUT="/tmp/systemctl-analysis-${SERVICE}-$(date +%F-%H%M%S)"

mkdir -p "$OUT"

systemctl --no-pager -l status "$SERVICE" \
  > "$OUT/status.txt" 2>&1

systemctl show "$SERVICE" \
  > "$OUT/show.txt" 2>&1

systemctl cat "$SERVICE" \
  > "$OUT/unit-file.txt" 2>&1

systemctl list-dependencies "$SERVICE" \
  > "$OUT/dependencies.txt" 2>&1

systemctl list-dependencies --reverse "$SERVICE" \
  > "$OUT/reverse-dependencies.txt" 2>&1

journalctl -b -u "$SERVICE" --no-pager \
  > "$OUT/journal-current-boot.txt" 2>&1

systemctl --failed --no-pager \
  > "$OUT/failed-units.txt" 2>&1

systemd-analyze critical-chain "$SERVICE" \
  > "$OUT/critical-chain.txt" 2>&1

tar -czf "${OUT}.tar.gz" -C "$(dirname "$OUT")" "$(basename "$OUT")"

echo "Created: ${OUT}.tar.gz"
```

Save as:

```text
/usr/local/sbin/collect-systemctl-analysis.sh
```

Make executable:

```bash
sudo chmod +x /usr/local/sbin/collect-systemctl-analysis.sh
```

Run:

```bash
sudo /usr/local/sbin/collect-systemctl-analysis.sh sshd
```

---

# Quick Command Cheat Sheet

```bash
# Service status
systemctl status sshd

# Start service
sudo systemctl start sshd

# Stop service
sudo systemctl stop sshd

# Restart service
sudo systemctl restart sshd

# Reload service
sudo systemctl reload sshd

# Reload if supported, otherwise restart
sudo systemctl reload-or-restart sshd

# Enable at boot
sudo systemctl enable sshd

# Enable and start now
sudo systemctl enable --now sshd

# Disable at boot
sudo systemctl disable sshd

# Disable and stop
sudo systemctl disable --now sshd

# Check active state
systemctl is-active sshd

# Check enablement
systemctl is-enabled sshd

# Check failed state
systemctl is-failed sshd

# List running services
systemctl list-units --type=service --state=running

# List all loaded services
systemctl list-units --type=service --all

# List installed service files
systemctl list-unit-files --type=service

# List failed units
systemctl --failed

# Reset failed state
sudo systemctl reset-failed sshd

# Show unit file and drop-ins
systemctl cat sshd

# Show unit properties
systemctl show sshd

# Edit with a drop-in
sudo systemctl edit sshd

# Reload unit definitions
sudo systemctl daemon-reload

# Mask service
sudo systemctl mask sshd

# Unmask service
sudo systemctl unmask sshd

# Show dependencies
systemctl list-dependencies sshd

# Show reverse dependencies
systemctl list-dependencies --reverse sshd

# Show default boot target
systemctl get-default

# Set text-mode target
sudo systemctl set-default multi-user.target

# Set graphical target
sudo systemctl set-default graphical.target

# List timers
systemctl list-timers --all

# List sockets
systemctl list-sockets --all

# Reboot
sudo systemctl reboot

# Power off
sudo systemctl poweroff

# Manage a user service
systemctl --user status myapp.service
```

---

# Summary

`systemctl` is the main Linux command for managing systemd services and units.

The most important daily commands are:

1. `systemctl status <service>`
2. `systemctl start <service>`
3. `systemctl stop <service>`
4. `systemctl restart <service>`
5. `systemctl reload <service>`
6. `systemctl enable <service>`
7. `systemctl disable <service>`
8. `systemctl enable --now <service>`
9. `systemctl is-active <service>`
10. `systemctl is-enabled <service>`
11. `systemctl --failed`
12. `systemctl cat <service>`
13. `systemctl show <service>`
14. `systemctl edit <service>`
15. `systemctl daemon-reload`
16. `systemctl list-dependencies <service>`
17. `systemctl list-timers --all`
18. `systemctl get-default`
19. `systemctl set-default <target>`
20. `systemctl reboot`

Recommended troubleshooting sequence:

```text
status → logs → unit configuration → application validation → dependencies → reload/restart → verification
```

Example:

```bash
systemctl status httpd
journalctl -b -u httpd
systemctl cat httpd
apachectl configtest
sudo systemctl reload-or-restart httpd
systemctl status httpd
```

---

## Reference Manual Pages

```bash
man systemctl
man systemd
man systemd.unit
man systemd.service
man systemd.target
man systemd.timer
man systemd.socket
man systemd.exec
man journalctl
```
