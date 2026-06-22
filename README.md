# Linux For Developers

A practical reference for developers working on Linux (Debian/Ubuntu-based).  
Covers everyday commands, system internals, networking, package management, and more.

> All examples are tested on **Ubuntu/Debian**. Most commands work on any Linux distro.

---

## Contents

### Core Concepts
| Topic | Description |
|---|---|
| [Kernel](Documentation/kernel.md) | Kernel version, loaded modules, kernel messages |
| [Linux Directory Structure](linux_directory_structure.md) | What every directory in `/` is for |
| [Filesystem & Partitions](Documentation/filesystem.md) | `fdisk`, partition schemes, GRUB |
| [Environment Variables](Documentation/env_var.md) | `env`, `export`, `printenv`, setting vars |
| [Path](Documentation/path.md) | How `$PATH` works, adding custom paths |
| [Memory](Documentation/memeory.md#memory) | `free`, `/proc/meminfo` |
| [Swap](Documentation/memeory.md#swap) | `mkswap`, `swapon`, `swapoff` |

### System Administration
| Topic | Description |
|---|---|
| [System Boot](Documentation/system_boot.md#system-boot) | `initramfs`, `vmlinuz`, init system |
| [System Runlevels](Documentation/system_boot.md#system-runlevels) | Runlevel table (0–6) |
| [Managing System Services](Documentation/System_services.md) | `systemctl` — start, stop, enable, disable |
| [Process Monitoring](Documentation/process.md) | `top`, `ps`, `uptime` |
| [Add New User](Documentation/adding%20User.md) | `useradd`, `passwd`, `userdel` |
| [Upgrading & Patching](Documentation/Upgrading%20and%20Patching.md) | `apt-get update`, `dist-upgrade`, package ops |
| [Using dpkg](Documentation/Using%20dpkg.md) | List, inspect, and remove Debian packages |

### Networking
| Topic | Description |
|---|---|
| [Network & Interfaces](Documentation/Networking.md) | `ip link`, set IP, bring interface up/down, routing |

### Shell & Productivity
| Topic | Description |
|---|---|
| [echo & cat](Documentation/echo_and_cat.md) | Create and write files from the command line |
| [Aliases](Documentation/aliases.md) | Define and manage shell shortcuts |

### Docker Internals
| Topic | Description |
|---|---|
| [Docker Internals](docker_internals.md) | Image vs Container, OverlayFS, iptables, cgroups, namespaces |
| [Kernel Deep Dive](kernel.md) | Kernel vs OS, kernel space vs user space, syscalls |

### Utilities
| Topic | Description |
|---|---|
| [Video Editing (FFmpeg)](Documentation/Video_editing.md) | Trim, merge, combine audio/video, speed up |
| [Check GPU Usage](Check%20gpu%20usage.md) | Intel (`intel_gpu_top`) and Nvidia (`nvidia-smi`) |
| [Convert PPTX to PDF](Convert%20all%20pptx%20to%20pdf.md) | Batch convert slides with `soffice` |

---

## Power User Commands

**Estimate total video duration of all MP4s in a directory:**
```bash
find . -iname '*.mp4' -exec ffprobe -v quiet -of csv=p=0 -show_entries format=duration {} \; \
  | paste -sd+ - | bc -l | awk '{printf "%.2f minutes\n", $1/60}'
```

**Find which process is using a port:**
```bash
sudo ss -tulnp | grep :8080
```

**Watch real-time network traffic per interface:**
```bash
watch -n 1 ip -s link
```

**List all open files by a process:**
```bash
lsof -p <PID>
```

**Check disk usage, sorted by size:**
```bash
du -h --max-depth=1 | sort -hr
```

---

## Quick Reference

```bash
# System info
uname -r              # kernel version
uname -a              # full system info
lsb_release -a        # distro info
uptime                # how long system has been running

# Disk
df -h                 # disk usage per filesystem
lsblk                 # block devices tree
sudo fdisk -l         # partition details

# Memory
free -mt              # RAM and swap summary
cat /proc/meminfo     # detailed memory info

# Processes
ps aux                # all running processes
top                   # live process monitor
kill -9 <PID>         # force kill a process

# Network
ip link               # all network interfaces
ip addr               # IP addresses
ping -c 4 google.com  # connectivity check
curl ifconfig.me      # your public IP

# Services
systemctl status <service>    # check service status
sudo systemctl restart nginx  # restart a service
journalctl -u nginx -f        # follow service logs
```
