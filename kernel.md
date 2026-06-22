# Kernel

## See the kernel version:

```bash
uname -r
```

## Which modules are loaded:

```bash
lsmod | less
```

## Get kernel messages:

```bash
dmesg
```

---

## What the Kernel Is

The kernel is the **first program that loads at boot** and never stops running.  
It is the only software that **directly talks to hardware**.

```
Hardware          →  Physical components (CPU, RAM, Disk, NIC)
Kernel            →  Controls hardware directly, always running
OS (full system)  →  Kernel + userland tools + utilities + GUI
Applications      →  Run in user space, request kernel services via syscalls
```

### 4 Core Responsibilities

| Responsibility | What It Does |
|---|---|
| **Process Management** | Creates, schedules, kills processes; handles multitasking across CPU cores |
| **Memory Management** | Allocates/frees RAM, enforces isolation, manages virtual memory |
| **Device Management** | Talks to hardware via device drivers (disk, NIC, keyboard, GPU) |
| **System Calls** | Only gateway for apps to request kernel services (`read`, `write`, `fork`, `socket`) |

---

## OS vs Kernel

```
┌──────────────────────────────────────────┐
│              USER SPACE                  │
│  Browser   IDE   Terminal   Docker CLI   │  ←  Applications
│  Package Manager   Shell   File Manager  │  ←  OS Utilities
│  GUI (GNOME/KDE)   System Libraries      │  ←  OS Layer
├──────────────────────────────────────────┤
│              KERNEL SPACE                │
│  Process Mgmt   Memory Mgmt   Scheduler  │
│  Device Drivers   Filesystem   Network   │  ←  THE KERNEL
└──────────────────────────────────────────┘
           ↕  direct hardware access
┌──────────────────────────────────────────┐
│   CPU    RAM    Disk    NIC    GPU       │  ←  Hardware
└──────────────────────────────────────────┘
```

| | Kernel | Operating System |
|---|---|---|
| What it is | Core engine | Kernel + tools + utilities |
| Hardware access | Direct | Always goes through kernel |
| Examples | Linux kernel, Windows NT | Ubuntu, Windows 11, macOS, Android |
| Runs in | Kernel space (privileged) | User space (restricted) |

### Linux ≠ Ubuntu

```
Linux    =  just the kernel  (Linus Torvalds, 1991)
Ubuntu   =  Linux kernel + apt + bash + GNOME + tools
Debian   =  Linux kernel + different tools/config
Android  =  Linux kernel + Google userspace + apps
```

---

## Kernel Space vs User Space

```
User Space    →  your app runs here  —  RESTRICTED  —  cannot touch hardware
                          ↓  system call  (the only bridge)
Kernel Space  →  kernel runs here  —  PRIVILEGED  —  full hardware access
```

### Example — Java reading a file

```java
Files.readString(Path.of("config.yaml"))
```

What actually happens:
```
Java IO  →  glibc  →  open() syscall  →  Linux kernel  →  disk driver  →  disk
```

Your code never touches the disk — the kernel does it on your behalf.

---

## How This Relates to Docker

```
VM:      App → [Guest Kernel] → [Hypervisor] → [Host Kernel] → Hardware
Docker:  App → [Host Kernel] → Hardware
```

- VM boots a full guest kernel → slow startup, heavy RAM usage
- Docker container has **no kernel** — shares host kernel directly
- Container lives in **user space**, isolated via namespaces
- No kernel to boot → container starts in milliseconds
