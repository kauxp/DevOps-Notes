# Understanding Kubernetes services

Short summary:

Kubernetes Services provide stable networking and discovery for pods.

What you'll learn:

- Service types: ClusterIP, NodePort, LoadBalancer
- Endpoints and selectors
- Service discovery patterns

Resources:

- Add links or notes here

Exercises:

- Expose an app using different Service types

Notes:

- Add instructor notes or references here

---

## Kubernetes Services — Personal Notes

### What is Kubernetes?

Kubernetes = container orchestration platform. It manages deploy, scale, heal, network, and rollout/rollback. Used to run applications across many machines (a cluster).

### Core building blocks

- Pod → smallest unit
- ReplicaSet → keeps N pods alive
- Deployment → manages ReplicaSets
- Service → stable network access
- LoadBalancer → external traffic entry

### Pod (smallest unit)

- Pod = one or more containers running together

All containers in a Pod:

- Share IP
- Share ports
- Share volumes

They are co-located and co-scheduled.

My note: Pod is like a house, containers are rooms, IP is for the house.

### ReplicaSet

- Ensures N copies of a Pod are always running. If a Pod dies, a new Pod is created. Always use a ReplicaSet/Deployment instead of raw Pods for safety. ReplicaSets are used internally by Deployments.

### Deployment

- Higher-level controller that manages ReplicaSets. Supports rolling updates, rollbacks, and scaling. Use Deployments in real life.

### Why we need Services

- Pods are ephemeral: IPs change on restart, and Pods scale up/down. You should never communicate with Pods directly.

### What is a Service?

- A Service provides a stable virtual IP and DNS name for a group of Pods. It offers load balancing and traffic routing via kube-proxy.

How it works (internals): create a Service with a selector (e.g., `app: backend`), kube-proxy programs iptables/IPVS rules, Service receives a ClusterIP, and traffic to the ClusterIP is forwarded to matching Pods.

### Types of Services

1. ClusterIP (default): internal-only, used for microservice-to-microservice or DB access.

2. NodePort: exposes a Service on NodeIP:NodePort (30000–32767). Flow: Internet → NodeIP:NodePort → Service → Pod. Useful for local clusters, testing, or bare-metal.

3. LoadBalancer (cloud): provisions a cloud provider load balancer (AWS/GCP/Azure). Flow: Internet → Cloud LB → NodePort → Service → Pod. Produces a public IP for production traffic.

### Headless Service

- `clusterIP: None` — used for StatefulSets and databases where direct Pod addressing or DNS SRV is required.

### Service DNS

- Every Service gets a DNS name: `service.namespace.svc.cluster.local`.

### YAML Structure

- Every Kubernetes object has: `apiVersion`, `kind`, `metadata`, `spec`.

### Final mental model

- Pod = worker
- ReplicaSet = supervisor
- Deployment = manager
- Service = reception desk
- LoadBalancer = main gate

One-line summary:

Kubernetes apps are deployed using Deployments, kept alive by ReplicaSets, and accessed through Services.
