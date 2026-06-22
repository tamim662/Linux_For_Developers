# Linux Directory Structure

```
/ (Root)
├── /home          User home directories
├── /root          Superuser home directory
├── /etc           System-wide config files
├── /tmp           Temporary files (cleared on reboot)
├── /dev           Device files
├── /var           Variable data: logs, cache, databases
├── /proc          Virtual FS: live kernel/process info
├── /sys           Virtual FS: hardware and kernel data
├── /bin           Essential user binaries
├── /sbin          System admin binaries (root only)
├── /lib           Shared libraries for /bin and /sbin
├── /lib64         64-bit shared libraries
├── /boot          Kernel image + bootloader
├── /opt           Optional/third-party software
├── /mnt           Temporary manual mount point
├── /media         Removable media (USB, CD)
├── /srv           Data served by the system (web, FTP)
└── /usr           Secondary hierarchy: user programs and data
```

---

## User Directories

| Directory | Purpose |
|---|---|
| `/home` | Personal files per user — e.g. `/home/tamim` |
| `/root` | Superuser home — separate from `/home` |

---

## System Directories

| Directory | Purpose |
|---|---|
| `/bin` | Core binaries all users need: `ls`, `cp`, `mv`, `bash`, `cat` |
| `/sbin` | Admin binaries root uses: `mount`, `fsck`, `reboot`, `ifconfig` |
| `/lib` | Shared libraries required by `/bin` and `/sbin` |
| `/lib64` | 64-bit versions of those libraries |
| `/boot` | Kernel image (`vmlinuz`), bootloader config (`GRUB`) |

---

## Config & Runtime

| Directory | Purpose |
|---|---|
| `/etc` | All system config — `/etc/nginx/`, `/etc/hosts`, `/etc/fstab` |
| `/tmp` | Temporary files — world-writable, auto-cleared on reboot |
| `/var` | Growing files — `/var/log/`, `/var/cache/`, databases, spool |
| `/dev` | Device files — everything is a file in Linux (`/dev/sda` = disk, `/dev/null`) |

---

## Virtual Filesystems (not real directories on disk)

| Directory | Purpose |
|---|---|
| `/proc` | Live kernel and process info — generated in memory by the kernel |
| `/sys` | Hardware info exposed by the kernel (devices, drivers, power) |

```bash
# See CPU info
cat /proc/cpuinfo

# See info about process with PID 1234
ls /proc/1234/

# See kernel messages
cat /proc/kmsg
```

> `/proc` and `/sys` are virtual — they don't exist on disk. The kernel generates them live.

---

## Optional & External

| Directory | Purpose |
|---|---|
| `/opt` | Self-contained third-party apps — e.g. `/opt/google/chrome` |
| `/mnt` | Temporary manual mount point for extra filesystems |
| `/media` | Auto-mount for removable media (USB drives, DVDs) |
| `/srv` | Data the machine serves — web root, FTP files |
| `/usr` | Large secondary hierarchy: `/usr/bin`, `/usr/lib`, `/usr/share` |

---

## How Docker Uses This Structure

When a container starts, it gets its own **isolated view** of the directory tree via namespaces + OverlayFS:

| Directory | In Container |
|---|---|
| `/etc` | From image layer (read-only) |
| `/bin` | From image layer (read-only) |
| `/var` | Writable via OverlayFS R/W layer |
| `/proc` | Container's own PID namespace view |
| `/dev` | Restricted device access |
| `/tmp` | Writable via OverlayFS R/W layer |

The container thinks it owns the entire filesystem — it's all namespaces and OverlayFS mapped by the host kernel.
