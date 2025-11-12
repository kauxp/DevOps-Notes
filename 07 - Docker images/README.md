# Docker images

Short summary:

How Docker images work and how to build and optimize them.

What you'll learn:

- Dockerfile syntax and best practices
- Layers, caching, multi-stage builds
- Image size optimization

Resources:

- Add links or notes here

Exercises:

- Create optimized multi-stage Dockerfile

Notes:

- Add instructor notes or references here

## Docker Image Deep Dive

### Part 1: Introduction — What Is a Docker Image?

A Docker image is (more than) a blueprint for a container. It’s a read-only, layered filesystem built using a Dockerfile. When you run an image with `docker run`, Docker creates a container — a running instance with a writable layer on top of the image.

### Part 2: Dockerfile Fundamentals

A Dockerfile is a recipe that defines how your image is built.

#### Minimal Dockerfile Example

```dockerfile
# Simple Dockerfile Example
FROM python:3.12-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

Let’s break down common Dockerfile instructions.

### 1. FROM — Base Image

```text
FROM <image>[:<tag>]
```

- Specifies the base image (starting point).
- Best practices: use lightweight images (`-alpine`, `-slim`), pin versions, use `scratch` for minimal builds.

### 2. RUN — Execute Commands During Build

```dockerfile
RUN apt-get update && apt-get install -y curl
RUN pip install flask
```

- Each `RUN` creates a new image layer.
- Best practice: combine related commands and clean caches.

### 3. CMD — Default Command to Run in Container

```dockerfile
CMD ["python", "app.py"]
```

- JSON (exec) form recommended.
- Only one `CMD` per Dockerfile.

### 4. ENTRYPOINT — Fixed Main Command

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

- Use `ENTRYPOINT` for the main executable and `CMD` for default args.

### 5. Combining ENTRYPOINT and CMD

Example:

```dockerfile
FROM ubuntu:22.04
ENTRYPOINT ["echo"]
CMD ["Hello, World!"]
```

Running `docker run myimage` → `Hello, World!`
Running `docker run myimage Goodbye` → `Goodbye`

### 6. ADD and COPY — Copying Files into the Image

- `COPY` — predictable local copy.
- `ADD` — supports URLs and automatic tar extraction; use only when needed.

### 7. ENV — Set Environment Variables

```dockerfile
ENV APP_ENV=production
ENV PORT=8080
```

- Use `ENV` for config that doesn't change frequently.

### Part 3: Building a Real-World Docker Image Example (Flask)

Directory structure:

```
flask-app/
├── app.py
├── requirements.txt
└── Dockerfile
```

app.py:

```python
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello():
    return "Hello from Docker!"
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

requirements.txt:

```
flask==3.0.2
```

Dockerfile:

```dockerfile
# Stage 1: Base
FROM python:3.12-slim
# Set working directory
WORKDIR /app
# Copy dependency file first for caching
COPY requirements.txt .
# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt
# Copy the rest of the source code
COPY . .
# Set environment variables
ENV PORT=8080
# Expose port
EXPOSE 8080
# Use entrypoint and cmd
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Build and run:

```bash
docker build -t flask-app:latest .
docker run -p 8080:8080 flask-app
```

Access at: `http://localhost:8080`

### Part 4: Docker Image Best Practices

Category | Best Practice | Why
--- | --- | ---
Base Image | Use minimal images (`-alpine`, `-slim`) | Reduces size and attack surface
Layers | Combine related `RUN`s | Fewer layers = smaller image
Caching | Copy dependency files early | Avoids reinstalling packages unnecessarily
Cleanup | Remove caches (e.g., `rm -rf /var/lib/apt/lists/*`) | Prevents bloat
Security | Use non-root user (`USER appuser`) | Prevents privilege escalation
Scanning | Use `docker scan` or Trivy | Detects vulnerabilities
Secrets | Don’t hardcode passwords or tokens | Use env vars or Docker secrets
Versioning | Pin base images and dependencies | Ensures reproducibility
Multi-stage Builds | Use multiple stages to reduce size | Only final stage goes to production
Healthcheck | Add `HEALTHCHECK` instruction | Detect and auto-restart unhealthy containers

#### Example — Secure Image Setup

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt && \
    useradd -m appuser
COPY . .
USER appuser
EXPOSE 8080
ENTRYPOINT ["python"]
CMD ["app.py"]
```

### Part 5: Bonus — Inspecting and Analyzing Your Image

- List image layers: `docker history flask-app`
- Inspect metadata: `docker inspect flask-app`
- Scan for vulnerabilities: `docker scan flask-app` or use Trivy: `trivy image flask-app`

References:

1. https://github.com/vilasvarghese/docker-k8s/dockerfiles
2. Command example:

```bash
docker build -t <image-name-in-lowercase> -f <file-name> .
```

## Docker Containers — Personal Notes

These are short, crisp study notes derived from DockerContainerNotes.pdf.

### What is a Container?

- A container is just:
  - A Linux process
  - Plus isolation (namespaces + cgroups)
- It is NOT:
  - A virtual machine
  - A mini OS
- It runs directly on the host kernel.

### Why Containers Are Ephemeral

- Each container has:
  - Read-only image layers
  - One temporary writable layer
- If the main process (PID 1) exits:
  - Container stops
  - Writable layer is deleted
  - All data is lost
- Therefore containers are stateless by default.

### Container = Just a Process

When we run:

```bash
docker run nginx
```

- Docker does NOT start a VM
- It starts a normal Linux process (nginx) inside namespaces for isolation.
- On the host, the container process is visible in `ps -ef`.

### Why Container Isolation is “Pseudo"

- All containers share the same host kernel.
- Isolation is done using namespaces (visibility) and cgroups (limits).
- Because the kernel is shared, containers are NOT as strong as VMs; kernel bugs can break isolation.

### Container Internals (High Level)

Architecture:

```text
docker → containerd → containerd-shim → runc → your app
```

- `containerd`: manages containers
- `containerd-shim`: parent of container process
- `runc`: sets up namespaces and starts the process

### Why Killing PID 1 Stops the Container

- The container lifecycle equals the lifecycle of PID 1.
- If PID 1 exits: container stops, filesystem layer is deleted, network and cgroups cleaned.

```bash
docker kill <container>
```

Kills PID 1 → container dies.

### Why Containers Look Like Small Machines

- Namespaces provide scoped resources:
  - PID namespace → own process tree
  - NET namespace → own IP, interfaces
  - MOUNT namespace → own filesystem root
  - UTS namespace → own hostname
  - IPC namespace → own shared memory
  - USER namespace → root inside ≠ root outside

But all of this is an illusion; the kernel is shared.

### Why Containers Are Fast

- No kernel boot, no hardware emulation — just a cloned process:

```text
runc → clone() → process starts
```

Startup time: milliseconds.

### Why Containers Are Lightweight

They share:

- Host kernel
- Host OS
- CPU scheduler
- Memory management

Only filesystem, process, and network views are isolated.

### Why Containers Die When App Exits

- Container = one main process
- If that main process exits, the container exits.

Examples:

- `nginx` dies → container dies
- `python app.py` exits → container exits
- `sleep 5` finishes → container exits

### Namespaces vs Cgroups

- Namespaces = isolation (what the process can see)
- Cgroups = limits (how much it can use)

Cgroups control CPU, memory, disk I/O, PIDs.

### How Docker Creates a Container

1. Pull image
2. Unpack layers (OverlayFS)
3. Create cgroups
4. Create namespaces
5. chroot into new root
6. Start process (e.g., `nginx`)

### VM vs Container

VM:

```text
Hardware → Hypervisor → Guest OS → App
```

Container:

```text
Hardware → Linux Kernel → App (isolated)
```

- Docker does NOT run another OS; it runs processes.

### Final Mental Model

- A container is a normal Linux process with a fake world created by namespaces and limits applied by cgroups.
- If the main process dies, the container dies.
