# Docker volumes

Short summary:

Persistent storage for containers using volumes and bind mounts.

What you'll learn:

- Types of Docker storage: volumes, bind mounts, tmpfs
- Creating and managing volumes
- Backup and restore strategies

Resources:

- Add links or notes here

Exercises:

- Persist app data across container restarts

Notes:

- Add instructor notes or references here

---

## Docker Volumes — Personal Notes

---

### Why Do We Even Need Volumes?

- Containers are ephemeral
- They have:
  - Read-only image
  - One writable layer
- When a container is removed:
  - The writable layer is deleted
  - All data in that layer is lost
- So: if data must survive a container lifecycle, use Volumes or Bind Mounts

Examples of data that would be lost if not persisted:

- /var/lib/mysql
- /usr/share/nginx/html
- /app/logs

---

### Types of Storage in Docker (4)

1. Anonymous Volume
2. Named Volume
3. Bind Mount
4. tmpfs (RAM only)

Quick mnemonic:

anonymous = auto
named = real storage
bind = dev work
tmpfs = secret/fast/temp

---

### 1. Anonymous Volume

- Created automatically by Docker
- Has a random name

Example:

```bash
docker run -d -v /data nginx
```

Docker will create a volume with a random name (for example: d3c9248bc5a8f...).

Use when:

- Short-lived containers
- You don’t care about the volume name

Note: the volume is only auto-removed if the container is started with --rm.

---

### 2. Named Volume (Most Important)

- Created by the user
- Managed by Docker
- Persists even if the container is deleted

Best for:

- Databases
- Uploads
- App data

Example:

```bash
docker volume create mydata

docker run -d -v mydata:/var/lib/mysql mysql:8
```

Even if the container is removed, the data in the named volume remains. This is the recommended approach for databases in production.

---

### 3. Bind Mount

- Mounts a host directory directly into the container
- Format: -v /host/path:/container/path

Example:

```bash
docker run -d -v $(pwd)/website:/usr/share/nginx/html nginx
```

Characteristics:

- Changes on host are instantly visible in the container
- Best for local development, config files, and logs

Notes:

- Host controls the folder and permissions
- Not typically recommended for production unless required

---

### 4. tmpfs Mount

- Stored in RAM (ephemeral)
- Very fast
- Data is lost when the container stops

Used for:

- Secrets
- Cache
- Temporary files

Example:

```bash
docker run -d --tmpfs /cache nginx
```

tmpfs = speed + security + no persistence

---

### Where Docker Stores Volumes? (Interview Point)

On a typical Linux host Docker stores volumes under:

/var/lib/docker/volumes/

Inside a named volume called mydata you will find data under:

mydata/_data/

Note: Docker manages these locations; bind mounts do not live under /var/lib/docker/volumes.

---

### Quick Comparison (In My Words)

- Anonymous → temporary, auto-created
- Named → persistent, managed by Docker
- Bind → host-backed, for development and config sync
- tmpfs → in-RAM, fast and ephemeral

---

### My Final Mental Model

- Containers die → ephemeral writable layer dies
- Volumes exist → data survives container restarts and recreation
- For DB → always use a Named Volume
- For development → use a Bind Mount
- For secrets/cache → prefer tmpfs

---

### One Line Summary

Volumes are how Docker stops containers from forgetting everything.
