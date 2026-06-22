# Docker & Linux Internals

> Personal reference notes covering Docker architecture, Linux kernel internals, and Linux filesystem structure.

---

## Table of Contents
1. [Docker Image vs Docker Container](#1-docker-image-vs-docker-container)
2. [What a Docker Container Actually Uses](#2-what-a-docker-container-actually-uses)
3. [OverlayFS — Layered Filesystem](#3-overlayfs--layered-filesystem)
4. [iptables — Network Traffic Routing](#4-iptables--network-traffic-routing)
5. [Linux Kernel vs Operating System](#5-linux-kernel-vs-operating-system)
6. [Kernel Space vs User Space](#6-kernel-space-vs-user-space)
7. [Linux Directory Structure](#7-linux-directory-structure)
8. [Everything Together — How Docker Uses Linux](#8-everything-together--how-docker-uses-linux)

---

## 1. Docker Image vs Docker Container

| | **Docker Image** | **Docker Container** |
|---|---|---|
| What it is | Read-only blueprint (frozen filesystem layers) | A running instance of an image |
| State | Static, immutable | Dynamic, has a live read-write layer |
| Analogy | A Java `.class` file | A running JVM process |
| Storage | Shared across containers | Adds a thin private R/W layer on top |

```
Image   =  blueprint  (frozen)
Container = blueprint + running process + read-write layer
```

---

## 2. What a Docker Container Actually Uses

A container does **NOT** have its own RAM, HDD, or network drivers.  
It **borrows and isolates** from the host OS using Linux kernel primitives.

| Hardware Concept | VM (Virtual Machine) | Docker Container |
|---|---|---|
| RAM | Allocated virtual RAM | Borrowed from host, capped by **cgroups** |
| Storage | Virtual disk (.vmdk) | Layered filesystem via **OverlayFS** |
| Network | Virtual NIC (emulated) | Virtual ethernet pair via **veth + docker0 bridge** |
| CPU | vCPU allocated | Host CPU, throttled by **cgroups** |
| OS Kernel | Full guest kernel | **Shared host kernel — no guest kernel** |

### The Three Linux Features Docker Uses

```
┌─────────────────────────────────────────────┐
│  Docker Container                           │
│                                             │
│  Namespaces  → Isolation (process, network) │
│  cgroups     → Resource limits (RAM, CPU)   │
│  OverlayFS   → Layered filesystem           │
└─────────────────────────────────────────────┘
```

> A container is just a **regular Linux process** with restricted visibility and resource limits.

---

## 3. OverlayFS — Layered Filesystem

**OverlayFS** is a *union filesystem* — it stacks multiple read-only layers and presents them as a single unified filesystem view.

### How Docker Layers Work

Each line in a `Dockerfile` (`RUN`, `COPY`, `ADD`) creates a new layer:

```
┌─────────────────────────────────┐  ← Read-Write layer (per container — discarded on delete)
├─────────────────────────────────┤  ← Your app layer (e.g., Spring Boot JAR)
├─────────────────────────────────┤  ← JDK layer
├─────────────────────────────────┤  ← Ubuntu base OS layer
└─────────────────────────────────┘  ← Scratch (empty base)
         READ-ONLY LAYERS (shared across containers)
```

### Copy-on-Write (CoW) Mechanism

If a container needs to **modify** a file from a read-only layer:
1. The file is **copied up** to the container's top R/W layer
2. Modification happens there
3. Lower layers remain **untouched and shared**

### Why This Is Efficient

- 10 containers from the same image **share** all read-only layers → saves disk space
- Each container only adds its own thin R/W layer on top
- Deleting a container just discards its thin top layer — shared layers are unaffected

---

## 4. iptables — Network Traffic Routing

**iptables** is a Linux kernel packet filter and router. It intercepts every network packet and decides: forward it, drop it, or modify it.

### Docker Port Mapping Under the Hood

When you run:
```bash
docker run -p 8080:8080 my-spring-app
```

Docker writes an **iptables DNAT rule** (Destination NAT):
```
"Any packet arriving on host:8080 → forward to container IP 172.17.0.2:8080"
```

### Full Network Flow

```
Browser Request → host:8080
                    ↓  iptables DNAT rule
                docker0 bridge (172.17.0.1)   ← virtual bridge on host
                    ↓  veth pair (virtual ethernet cable)
                container eth0 (172.17.0.2:8080)
                    ↓
                Spring Boot app
```

### Key Components

| Component | Role |
|---|---|
| `docker0` | Virtual network bridge Docker creates on host |
| `veth pair` | Virtual ethernet cable between container and bridge |
| `iptables` | Handles port mapping (DNAT) and routing rules |
| `DNAT` | Rewrites the destination IP/port of incoming packets |

---

## 5. Linux Kernel vs Operating System

### The Restaurant Analogy

```
Hardware          →  Kitchen equipment (stove, oven, fridge)
Kernel            →  Head chef (directly controls equipment)
OS (full system)  →  The entire restaurant (chef + waiters + menus + tables)
Applications      →  Customers placing orders
```

### What the Kernel Is

The kernel is the **first program that loads at boot** and **never stops running**.  
It is the **only software that directly talks to hardware**.

#### 4 Core Responsibilities of the Kernel

| Responsibility | What It Does |
|---|---|
| **Process Management** | Creates, schedules, and kills processes; handles multitasking |
| **Memory Management** | Allocates/frees RAM, enforces isolation between processes, manages virtual memory |
| **Device Management** | Talks to hardware via device drivers (keyboard, disk, NIC, GPU) |
| **System Calls** | The only gateway for apps to request kernel services (`read`, `write`, `fork`, `socket`) |

### OS vs Kernel — The Clear Difference

```
┌──────────────────────────────────────────┐
│              USER SPACE                  │
│  Browser   IDE   Terminal   Docker CLI   │  ← Applications
│  File Manager  Package Manager  Shell    │  ← OS Utilities
│  GUI (GNOME/KDE)  System Libraries       │  ← OS Layer
├──────────────────────────────────────────┤
│              KERNEL SPACE                │
│  Process Mgmt  Memory Mgmt  Scheduler    │
│  Device Drivers  Filesystem  Networking  │  ← THE KERNEL
└──────────────────────────────────────────┘
         ↕  (direct hardware access)
┌──────────────────────────────────────────┐
│   CPU    RAM    Disk    NIC    GPU       │  ← Hardware
└──────────────────────────────────────────┘
```

| | **Kernel** | **Operating System** |
|---|---|---|
| What it is | Core engine | Kernel + all tools/utilities on top |
| Hardware access | Direct | Goes through kernel |
| Examples | Linux kernel, Windows NT kernel | Ubuntu, Windows 11, macOS, Android |
| Runs in | Kernel space (privileged) | User space (restricted) |

### Linux ≠ Ubuntu

```
Linux   =  just the kernel  (created by Linus Torvalds)
Ubuntu  =  Linux kernel + apt + bash + GNOME + 1000s of tools
Debian  =  Linux kernel + different tools/configs
Android =  Linux kernel + Google userspace + apps
```

Same kernel — completely different OS experience.

---

## 6. Kernel Space vs User Space

This is a critical security boundary in Linux:

```
User Space    → Your app runs here — RESTRICTED — cannot touch hardware directly
                         ↓  (system call — the only bridge)
Kernel Space  → Kernel runs here — PRIVILEGED — full hardware access
```

### Real Example — Java Reading a File

```java
Files.readString(Path.of("config.yaml"))  // Your Java code
```

What actually happens under the hood:
```
Java IO library
    → glibc (C standard library)
        → open() syscall  ← crosses into kernel space
            → Linux kernel
                → disk device driver
                    → physical disk
```

Your Java code **never** touches the disk. The kernel does it on your behalf via a **system call**.

### Why Docker Shares the Host Kernel

```
VM:      [App] → [Guest Kernel] → [Hypervisor] → [Host Kernel] → Hardware
Docker:  [App] → [Host Kernel] → Hardware
```

- VM boots a full guest kernel → takes minutes, uses GBs of RAM
- Docker container has **no kernel to boot** → starts in milliseconds
- Docker containers just live in **user space**, isolated via namespaces

---

## 7. Linux Directory Structure

> Based on the Filesystem Hierarchy Standard (FHS)

```
/ (Root Directory)
├── /home          ← User home directories (e.g., /home/tamim)
├── /root          ← Home directory for the root (superuser) account
├── /etc           ← System-wide configuration files
├── /tmp           ← Temporary files (cleared on reboot)
├── /dev           ← Device files (hardware represented as files)
├── /var           ← Variable data: logs, caches, databases
├── /proc          ← Virtual filesystem: live kernel/process info
├── /sys           ← Virtual filesystem: hardware and kernel data
├── /bin           ← Essential user binaries (ls, cp, cat, bash)
├── /sbin          ← System admin binaries (fdisk, ifconfig, mount)
├── /lib           ← Shared libraries for /bin and /sbin
├── /lib64         ← 64-bit shared libraries
├── /boot          ← Bootloader files, Linux kernel image
├── /opt           ← Optional/third-party software
├── /mnt           ← Temporary mount point for filesystems
├── /media         ← Mount point for removable media (USB, CD)
├── /srv           ← Data served by the system (web, FTP)
└── /usr           ← Secondary hierarchy: user programs and data
```

### Directory Categories (from the diagram)

#### 👤 User Directories
| Directory | Purpose |
|---|---|
| `/home` | Personal files for each regular user (`/home/tamim`, `/home/john`) |
| `/root` | Home for the superuser (root account) — separate from `/home` |

#### ⚙️ System Directories
| Directory | Purpose |
|---|---|
| `/bin` | Core binaries every user needs (`ls`, `cp`, `mv`, `bash`) |
| `/sbin` | System binaries only root uses (`mount`, `fsck`, `reboot`) |
| `/lib` | Shared libraries required by `/bin` and `/sbin` |
| `/lib64` | 64-bit versions of shared libraries |
| `/boot` | Kernel image (`vmlinuz`), bootloader (`GRUB`) config |

#### 📁 Config & Runtime
| Directory | Purpose |
|---|---|
| `/etc` | All system configuration files (`/etc/nginx/`, `/etc/hosts`) |
| `/tmp` | Temporary files, world-writable, auto-cleared on reboot |
| `/var` | Files that grow: logs (`/var/log`), spool, cache, databases |
| `/dev` | Device files — everything is a file in Linux (e.g., `/dev/sda` = disk) |

#### 🖥️ System Information (Virtual Filesystems)
| Directory | Purpose |
|---|---|
| `/proc` | Live process and kernel info (e.g., `/proc/cpuinfo`, `/proc/1234/` = process 1234) |
| `/sys` | Hardware info exposed by the kernel (devices, drivers, power management) |

> `/proc` and `/sys` are **not real directories on disk** — they are virtual filesystems generated live by the kernel.

#### 🔌 Optional & External
| Directory | Purpose |
|---|---|
| `/opt` | Self-contained third-party apps (e.g., `/opt/google/chrome`) |
| `/mnt` | Temporary manual mount point for extra filesystems |
| `/media` | Auto-mount point for removable media (USB drives, DVDs) |
| `/srv` | Data served by the machine (web root, FTP files) |
| `/usr` | Large secondary hierarchy: `/usr/bin`, `/usr/lib`, `/usr/share` |

### How Docker Uses the Linux Directory Structure

When a Docker container starts, it gets its own **isolated view** of this directory tree via **namespaces + OverlayFS**:

```
Host /         Container /
├── /etc   →   /etc   (from image layer, read-only)
├── /bin   →   /bin   (from image layer, read-only)
├── /var   →   /var   (writable — OverlayFS R/W layer)
├── /proc  →   /proc  (container's own PID namespace view)
├── /dev   →   /dev   (restricted device access)
└── /tmp   →   /tmp   (writable — OverlayFS R/W layer)
```

The container thinks it owns the entire filesystem — but it's actually OverlayFS layers mapped by the host kernel.

---

## 8. Everything Together — How Docker Uses Linux

When you run a Spring Boot container:

```bash
docker run -p 8080:8080 --memory=512m --cpus=1 my-spring-app
```

Here's what Linux provides for each concern:

| Concern | Linux Feature Used |
|---|---|
| Container has its own filesystem (`/`, `/etc`, `/app`) | **OverlayFS** (layered R/W on top of image layers) |
| Container can't see other containers' processes | **PID namespace** |
| Container has its own IP address | **Network namespace + veth pair** |
| Port 8080 maps from host to container | **iptables DNAT rule** |
| Container is limited to 512MB RAM | **cgroups** memory limit |
| Container is limited to 1 CPU | **cgroups** CPU quota |
| Container thinks it's its own machine | **UTS namespace** (own hostname) |
| `/proc` shows only container processes | **PID + mount namespace** |

### VM vs Docker — Final Comparison

```
Physical Machine:
  App → Real RAM | Real NIC | Real Disk | Own Kernel

Virtual Machine:
  App → [Guest Kernel] → [Hypervisor emulates: RAM | NIC | Disk] → Host Hardware

Docker Container:
  App → [Host Kernel provides: cgroups | namespaces | OverlayFS | iptables] → Host Hardware
```

Docker is fast and lightweight because it **skips the virtual hardware layer entirely** and speaks directly to the Linux kernel.

---

*Notes compiled from hands-on study of Docker architecture, Linux kernel internals, and the Linux Filesystem Hierarchy Standard (FHS).*
