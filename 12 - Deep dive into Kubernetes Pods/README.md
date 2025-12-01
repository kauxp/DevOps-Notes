# Deep dive into Kubernetes Pods

Short summary:

In-depth look at Pods, their lifecycle, and use-cases.

What you'll learn:

- Pod anatomy and multi-container patterns
- Init containers, lifecycle hooks, and probes
- Resource requests/limits

Resources:

- Add links or notes here

Exercises:

- Create pods with init containers and probes

Notes:

- Add instructor notes or references here

---

## Kubernetes Pods & Architecture — Personal Notes

### Big Picture

- Kubernetes = container orchestration system
- Architecture:
  - Control Plane = brain
  - Worker Nodes = do the work
- Master node runs control plane
- Worker nodes run pods

---

### How Cluster Was Created (From Class)

- 3 machines:
  - 1 Master
  - 2 Workers

- On master:

```bash
kubeadm init
```

- This installs:
  - API server
  - etcd
  - scheduler
  - controllers

---

### What is a Pod?

> Pod = smallest deployable unit in Kubernetes

- A pod = one or more containers
- All containers in a pod are:
  - Co-located
  - Co-scheduled
  - Share:
    - Network
    - Volumes
    - IPC

My note:
> Pod is the real unit Kubernetes manages, not containers.

---

### Control Plane Components (Brain)

- kube-apiserver:
  - Front door of cluster
  - kubectl talks only to this

- etcd:
  - Key-value store
  - Stores whole cluster state

- scheduler:
  - Picks node for pod

- controller-manager:
  - Keeps fixing mismatch between desired and actual state

---

### Worker Node Components

- kubelet:
  - Node agent
  - Actually creates pods

- container runtime:
  - containerd / CRI-O

- kube-proxy:
  - Handles service networking

---

### How kubectl Talks to Cluster

```text
kubectl → API Server → etcd
```

- API server does:
  - Auth
  - Validation
  - Admission checks
  - Stores in etcd

---

### Pod Creation Flow (Important Exam Flow)

1. kubectl apply -f pod.yaml
2. API server:
   - Auth
   - Admission
   - Stores pod in etcd
3. Scheduler:
   - Sees pod without node
   - Picks best node
   - Writes binding
4. Kubelet on that node:
   - Pulls image
   - Creates network (CNI)
   - Creates containers
5. Kubelet updates status in API server
6. Pod becomes Running → Ready

My mental model:
> User asks → API records → Scheduler decides → Kubelet executes

---

### What is Stored in etcd

- Everything:
  - Pod spec
  - Pod status
  - Node info
  - Config
  - Secrets

- Path like:

```text
/registry/pods/<ns>/<pod>
```

---

### Why etcd is Super Critical

> If etcd is gone = cluster is gone

So:
- Backups are mandatory

---

### Pod Networking

- kubelet calls CNI plugin
- Pod gets:
  - Its own IP
  - Its own network namespace
- kube-proxy programs iptables for services
- CoreDNS gives DNS for services

---

### Pod Lifecycle States

- Pending
- Running
- Succeeded
- Failed
- Unknown

---

### Probes

- Readiness:
  - If fail → pod removed from service traffic
- Liveness:
  - If fail → container restarted

---

### Init Containers

- Run before main containers
- Must succeed before app starts

---

### Namespaces

- Used for isolation
- Default namespaces:
  - default
  - kube-system
  - kube-public

---

### Useful Commands (Daily Life)

```bash
kubectl get pods
kubectl describe pod <pod>
kubectl get pods -o wide
kubectl get nodes
kubectl get events
kubectl logs <pod>
```

---

### Common Failure Patterns

- Pod stuck Pending:
  - No resources
  - Scheduling issue
- ImagePullBackOff:
  - Wrong image name
  - No credentials
- CrashLoopBackOff:
  - App crashing
- ContainerCreating forever:
  - CNI or volume issue

---

### My Final Mental Model

- etcd = memory
- API server = gatekeeper
- scheduler = decision maker
- kubelet = hands
- controller = auto-fixer
- Pod = smallest real unit

---

### One Line Summary

> Kubernetes is a system where you declare what you want, and many controllers + kubelets work together to make it true.
