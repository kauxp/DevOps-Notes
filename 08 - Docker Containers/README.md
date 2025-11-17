# Docker Containers — Personal Notes

These are my short, crisp study notes made from DockerContainerNotes.pdf.

---

## What is a Container?

- A container is just:
  - A Linux process
  - Plus isolation (namespaces + cgroups)
- It is NOT:
  - A virtual machine
  - A mini OS
- It runs directly on the host kernel.

---

## Why Containers Are Ephemeral

- Each container has:
  - Read-only image layers
  - One temporary writable layer
- If the main process (PID 1) exits:
  - Container stops
  - Writable layer is deleted
  - All data is lost
- Therefore containers are stateless by default.

---

## Container = Just a Process

When we run:

```bash
docker run nginx
```

- Docker does NOT start a VM
- It starts a normal Linux process:
  - nginx
- But inside namespaces for isolation.

On the host:

- The container process is visible in `ps -ef`
- It is just another process.

---

## Why Container Isolation is “Pseudo”

- All containers:
  - Share the same host kernel
- Isolation is done using:
  - Namespaces (visibility)
  - Cgroups (limits)
- Because kernel is shared:
  - Containers are NOT as strong as VMs
  - Kernel bugs can break isolation

So:

- Containers = process-level isolation
- Not OS-level isolation

---

## Container Internals (High Level)

Architecture:

```text
docker → containerd → containerd-shim → runc → your app
```

- containerd: manages containers
- shim: parent of container process
- runc: sets up namespaces and starts the process

---

## Why Killing PID 1 Stops the Container

- The container lifecycle = lifecycle of PID 1
- If PID 1 exits:
  - container stops
  - filesystem layer is deleted
  - network and cgroups cleaned

So:

```bash
docker kill <container>
```

Kills PID 1 → container dies.

---

## Why Containers Look Like Small Machines

Because of namespaces:

- PID namespace → own process tree
- NET namespace → own IP, interfaces
- MOUNT namespace → own filesystem root
- UTS namespace → own hostname
- IPC namespace → own shared memory
- USER namespace → root inside ≠ root outside

But all of this is illusion.

---

## Why Containers Are Fast

- No kernel boot
- No hardware emulation
- No BIOS
- Just:

```text
runc → clone() → process starts
```

Startup time: milliseconds.

---

## Why Containers Are Lightweight

They share:

- Host kernel
- Host OS
- CPU scheduler
- Memory management

Only:

- Filesystem view
- Process view
- Network view

Are isolated.

---

## Why Containers Die When App Exits

Because:

- A container = one main process

Examples:

- nginx dies → container dies
- python app.py exits → container exits
- sleep 5 finishes → container exits

---

## Namespaces vs Cgroups

- Namespaces = isolation (what the process can see)
- Cgroups = limits (how much it can use)

Cgroups control:

- CPU
- Memory
- Disk I/O
- PIDs

---

## How Docker Creates a Container

1. Pull image
2. Unpack layers (OverlayFS)
3. Create cgroups
4. Create namespaces
5. chroot into new root
6. Start process (e.g., nginx)

---

## VM vs Container

VM:

```text
Hardware → Hypervisor → Guest OS → App
```

Container:

```text
Hardware → Linux Kernel → App (isolated)
```

- Docker does NOT run another OS
- It runs processes.

---

## Final Mental Model

- A container is:
  - A normal Linux process
  - With a fake world created by namespaces
  - And limits applied by cgroups
- If the main process dies:
  - The container dies.
If the main process dies:
  - The container dies.
