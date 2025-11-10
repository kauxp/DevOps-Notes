# Introduction to Docker

Short summary:

Basics of containerization using Docker: concepts and simple workflows.

What you'll learn:

- Containers vs VMs
- Images, containers, registries
- Basic Docker CLI commands

Resources:

- Add links or notes here

Exercises:

- Build and run a simple containerized app

Notes:

- Add instructor notes or references here

## Docker — Complete Introduction Tutorial

## 1. What is Docker?

### Definition

Docker is an open-source platform designed to build, package, ship, and run applications in containers.

A container is a lightweight, standalone, and executable software package that includes everything needed to run an application:

- Code
- Runtime
- Libraries
- System tools
- Configuration

This ensures that an app runs the same way everywhere — on your laptop, a server, or in the cloud.

## 2. Why Do We Need Docker?

### Before Docker (Traditional Deployment)

- You’d install your app + dependencies directly on the OS.
- Different apps might require different library versions (conflicts).
- Deploying the same app on another machine often required manual setup again.

### With Docker

- Each app runs in its own container, isolated from others.
- Containers include all dependencies, so the environment is consistent everywhere.
- You can start, stop, copy, or remove containers easily.

## 3. Key Concepts and Components

Concept | Description
--- | ---
Image | A read-only blueprint that defines what a container is. Built from a Dockerfile.
Container | A running instance of an image. You can create, start, stop, or delete containers.
Dockerfile | A text file with instructions to build an image (e.g., which OS, dependencies, commands).
Docker Engine | The runtime that builds and runs containers.
Docker Hub | Public repository of images (like GitHub for code).
Docker Compose | Tool for defining and running multi-container applications (`docker-compose.yml`).

## 4. Docker Architecture

Client-Server Model (high-level):

- Docker CLI (client) talks to the Docker Daemon (server).
- The daemon builds, runs, and manages containers.

```
+----------------------------------------+
| Docker CLI (Client)                    |
+----------------------------------------+
             ↓ talks to
+----------------------------------------+
| Docker Daemon (Server)                 |
| - Builds, runs, and manages containers |
+----------------------------------------+
             ↓
+----------------------------------------+
| Container Runtime Layer                |
| - Images                               |
| - Containers                           |
+----------------------------------------+
             ↓
+----------------------------------------+
| Host Operating System                  |
+----------------------------------------+
```

Reference: Docker docs and community articles.

## 5. Installing Docker

Official convenience script (use with care):

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Or on Debian/Ubuntu via package manager:

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
docker --version
docker run hello-world
```

## 6. Basic Docker Commands

Task | Command | Description
--- | --- | ---
Check version | `docker --version` | Verify Docker installation
Run a container | `docker run hello-world` | Test setup
List containers | `docker ps` | Active containers
List all (including stopped) | `docker ps -a` | All containers
List images | `docker images` | Installed images
Pull image | `docker pull nginx` | Download from Docker Hub
Run interactive | `docker run -it ubuntu bash` | Open terminal inside Ubuntu
Stop container | `docker stop <container_id>` | Graceful stop
Remove container | `docker rm <container_id>` | Delete container
Remove image | `docker rmi <image_id>` | Delete image

## 7. What is a Docker Image?

A Docker image is a read-only, layered template used to create Docker containers. Each image contains everything needed to run a piece of software — code, runtime, system libraries, environment variables, and configuration files.

### Layered File System (Union File System)

Docker images are made up of multiple layers, stacked on top of each other using a union file system (like OverlayFS, AUFS, or Btrfs). Each layer represents a set of filesystem changes (add, modify, delete files).

Example Dockerfile steps map to layers:

- `FROM ubuntu:22.04` → Base layer
- `RUN apt-get install nginx -y` → Adds a new layer
- `COPY . /var/www/html` → Adds another layer
- `CMD ["nginx", "-g", "daemon off;"]` → Final metadata layer

Each `RUN`, `COPY`, or `ADD` command in a Dockerfile creates a new layer.

### Visual: Docker Image Layer Architecture

```
+------------------------------------+
| CMD / ENTRYPOINT (metadata layer)  |
+------------------------------------+
| COPY app files / configs           |
+------------------------------------+
| RUN apt-get install nginx          |
+------------------------------------+
| FROM ubuntu:22.04 (Base OS Layer)  |
+------------------------------------+
```

When you start a container, Docker adds a read-write layer on top of these read-only layers:

```
+------------------------------------+
| Writable Container Layer (diffs)   |
+------------------------------------+
| Read-only Image Layers (base img)  |
+------------------------------------+
```

### How Union File System Works

UnionFS merges all these layers into a single unified view. If a file exists in multiple layers, Docker uses the topmost copy (copy-on-write). If a file is modified or deleted in a container, it’s copied from the read-only layer into the writable layer first, then modified there.

### Where Are Docker Images Stored?

Depends on the storage driver and OS. On Linux (default `overlay2`), images and layers are stored under `/var/lib/docker/overlay2/`.

Typical structure:

```
/var/lib/docker/
├── overlay2/
│   ├── <layer_id>/
│   │   ├── diff/    ← actual filesystem changes
│   │   ├── lower/   ← references to parent layers
│   │   └── work/    ← temp area used by OverlayFS
├── image/
│   └── overlay2/
│       ├── imagedb/
│       │   └── content/sha256/  ← image metadata
│       └── layerdb/            ← layer relationships
```

### Inspecting Layers

```bash
docker image inspect <image_name> --format='{{json .RootFS.Layers}}' | jq
docker history <image_name>
```

Example `docker history` output shows each layer and its size.

### Layer Reuse and Caching

Docker uses content-addressable storage — layers identified by SHA256 hashes. If two images share the same base layers, Docker reuses them rather than duplicating, making builds faster and saving disk space.
