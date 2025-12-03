# Deep dive into Kubernetes Replicasets

Short summary:

Understanding ReplicaSets and how they ensure pod availability.

What you'll learn:

- ReplicaSet purpose and selectors
- Relationship with Deployments
- Scaling and rolling updates

Resources:

- Add links or notes here

Exercises:

- Create and scale ReplicaSets manually

Notes:

- Add instructor notes or references here

---

## ReplicaSet — Personal Notes (expanded)

### What is a ReplicaSet (RS)?

- A ReplicaSet is a Kubernetes controller whose job is to always keep N Pods running. It continuously compares the desired state (`spec.replicas`) with the current running Pods and creates or deletes Pods to match the desired count. The RS itself never runs Pods directly; it manages Pod objects.

### Basic ReplicaSet YAML (mind map)

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: <name>
spec:
  replicas: <N>
  selector:
    matchLabels:
      app: <label>
  template:
    metadata:
      labels:
        app: <label>
    spec:
      containers: ...
```

- Flow: RS stores the Pod template and creates Pods from it.

### What happens when you apply YAML?

- `kubectl apply` → API Server authenticates, authorizes, validates and stores the ReplicaSet object in etcd.
- The ReplicaSet controller sees desired=3 and current=0 and creates 3 Pod objects.

### Control plane flow (super important)

- User runs `kubectl` → API Server → etcd
- ReplicaSet controller detects differences and creates Pod objects
- Scheduler assigns Pods to nodes
- kubelet runs containers via the container runtime

Interview-worthy line: "Kubernetes works on a reconciliation loop."

### Pod lifecycle under ReplicaSet

- Pod created → Pending
- Scheduler schedules node → Scheduled
- kubelet pulls image, creates container, starts it → Pod becomes Running

### Continuous reconciliation loop

- RS lists Pods matching its selector, compares desired vs current, and fixes mismatches.

### Failure scenarios (exam favorites)

- Pod crashes: RS notices running < desired and creates replacements.
- Node dies: Pods become Unknown; RS deletes them and creates new ones on healthy nodes.
- Manual pod deletion: RS immediately creates a replacement.
- Deleting ReplicaSet: pods owned by it are deleted.

### How Kubernetes stores this in etcd (conceptual)

- `/registry/replicasets/.../<name>`
- `/registry/pods/.../<pod>`
- Each Pod object has an `ownerReference` pointing to the ReplicaSet and contains fields like `spec.nodeName` and `status`.

### One-line summary

- ReplicaSet = controller that ensures correct number of Pods using a reconciliation loop.

### Tiny memory tricks

- RS = Count Keeper
- Scheduler = Node Allotter
- Kubelet = Node Manager
- etcd = Cluster Memory
- API Server = Brain
