# Linux Important Commands — Topics 126 to 149

This package contains one Corporate/L3 Markdown guide for every requested topic.

## Naming and interpretation notes

- Numbering is preserved from 126 through 149.
- Slash characters were converted to spaces in filenames because `/` is a directory separator.
- `138. proc lock.md` documents the correct standard kernel path, `/proc/locks`.
- `132. sys block DEVICE queue scheduler.md` documents `/sys/block/DEVICE/queue/scheduler`.
- The `strace` guide is retained as topic 133 even though the command appeared in an earlier package.
- `grub2-install` contains strict BIOS/UEFI/Secure Boot warnings because procedures are distribution-specific.

## Files

- [126. nc.md](126.%20nc.md)
- [127. logrotate.md](127.%20logrotate.md)
- [128. lsof.md](128.%20lsof.md)
- [129. run-parts.md](129.%20run-parts.md)
- [130. crontab.md](130.%20crontab.md)
- [131. bpftrace.md](131.%20bpftrace.md)
- [132. sys block DEVICE queue scheduler.md](132.%20sys%20block%20DEVICE%20queue%20scheduler.md)
- [133. strace.md](133.%20strace.md)
- [134. curl.md](134.%20curl.md)
- [135. promtool.md](135.%20promtool.md)
- [136. exportfs.md](136.%20exportfs.md)
- [137. showmount.md](137.%20showmount.md)
- [138. proc lock.md](138.%20proc%20lock.md)
- [139. lslocks.md](139.%20lslocks.md)
- [140. tar.md](140.%20tar.md)
- [141. restorecon.md](141.%20restorecon.md)
- [142. rsync.md](142.%20rsync.md)
- [143. fsfreeze.md](143.%20fsfreeze.md)
- [144. xfs_freeze.md](144.%20xfs_freeze.md)
- [145. sync.md](145.%20sync.md)
- [146. restic.md](146.%20restic.md)
- [147. find.md](147.%20find.md)
- [148. borg.md](148.%20borg.md)
- [149. grub2-install.md](149.%20grub2-install.md)

## Safety

Some examples create listeners, alter storage scheduling, reload NFS exports, freeze filesystems, prune backup repositories, recursively delete files, or write bootloader data. Use a lab first, preserve evidence, maintain backups and console access, and follow change control.
