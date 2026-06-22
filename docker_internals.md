# Docker Internals

## Image vs Container

| | Image | Container |
|---|---|---|
| What it is | Read-only blueprint | Running instance of an image |
| State | Static, frozen | Live process with R/W layer on top |
| Analogy | Java `.class` file | Running JVM process |
| On delete | Stays on disk | R/W layer is discarded |

```
Image     =  frozen filesystem layers  (shared)
Container =  image + running process + thin read-write layer on top
```

---

## What a Container Actually Uses

A container does **not** own RAM, disk, or a network driver.  
It **borrows and isolates** host resources via Linux kernel primitives.

| Resource | VM | Docker Container |
|---|---|---|
| RAM | Allocated virtual RAM | Host RAM, capped by **cgroups** |
| Storage | Virtual disk (.vmdk) | Layered filesystem via **OverlayFS** |
| Network | Emulated NIC | Virtual ethernet pair (**veth** + `docker0` bridge) |
| CPU | vCPU allocated | Host CPU, throttled by **cgroups** |
| OS Kernel | Full guest kernel | **Shared host kernel — no guest kernel at all** |

### Three Linux features Docker relies on

```
Namespaces  →  Isolation  (own PID tree, own network, own filesystem view)
cgroups     →  Resource limits  (RAM cap, CPU quota)
OverlayFS   →  Layered filesystem  (image layers + container R/W layer)
```

> A container is just a regular Linux process with restricted visibility and resource limits.

---

## OverlayFS

A **union filesystem** that stacks read-only layers and presents them as one unified filesystem.

### How image layers work

Each Dockerfile instruction (`RUN`, `COPY`, `ADD`) creates a layer:

```
┌──────────────────────────────────┐  ← R/W layer  (per container, discarded on delete)
├──────────────────────────────────┤  ← App layer  (e.g. Spring Boot JAR)
├──────────────────────────────────┤  ← JDK layer
├──────────────────────────────────┤  ← Ubuntu base layer
└──────────────────────────────────┘  ← scratch
          READ-ONLY  (shared across all containers of this image)
```

### Copy-on-Write (CoW)

When a container modifies a file from a read-only layer:
1. File is **copied up** to the container's R/W layer
2. Modification happens there
3. Lower layers stay **untouched and shared**

Why this matters:
- 10 containers from the same image share all read-only layers → saves disk
- Deleting a container only discards its thin top layer

---

## iptables

Linux kernel packet filter and router.  
Docker uses it to handle **port mapping** between host and container.

### What happens when you run:

```bash
docker run -p 8080:8080 my-spring-app
```

Docker writes an iptables **DNAT rule**:
```
Any packet arriving on host:8080  →  forward to container IP 172.17.0.2:8080
```

### Full network path

```
Browser → host:8080
              ↓  iptables DNAT
          docker0 bridge (172.17.0.1)   ← virtual bridge on host
              ↓  veth pair
          container eth0 (172.17.0.2:8080)
              ↓
          Spring Boot app
```

| Component | Role |
|---|---|
| `docker0` | Virtual network bridge Docker creates on the host |
| `veth pair` | Virtual ethernet cable between container and bridge |
| `iptables` | Rewrites destination IP/port for incoming packets (DNAT) |

---

## VM vs Docker — Final Comparison

```
Physical Machine:   App → Real RAM | Real NIC | Real Disk | Own Kernel

VM:                 App → [Guest Kernel] → [Hypervisor] → [Host Hardware]

Docker Container:   App → [Host Kernel: cgroups + namespaces + OverlayFS + iptables] → [Host Hardware]
```

Docker is fast because it **skips the virtual hardware layer entirely** — no kernel to boot.

---

## Useful Commands

```bash
# See container resource usage (RAM, CPU)
docker stats

# Inspect network settings of a running container
docker inspect <container_id> | grep -i network

# View OverlayFS mount layers for a container
docker inspect <container_id> | grep -i graphdriver -A 10

# Check iptables rules Docker wrote
sudo iptables -t nat -L -n

# See running container processes on host (they're just Linux processes)
ps aux | grep <container_name>
```
