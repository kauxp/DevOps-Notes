# Deep dive into Kubernetes Deployments

Short summary:

Deployments provide declarative updates for pods and ReplicaSets.

What you'll learn:

- Deployment strategies (rolling, recreate)
- Rollbacks and revision history
- Best practices for updates

Resources:

- Add links or notes here

Exercises:

- Create a Deployment and perform rolling updates

Notes:

- Add instructor notes or references here

---

## Deployments — Personal Notes (expanded)

### Big Picture

- Pod:
  - Smallest unit in Kubernetes
  - Runs one or more containers
  - Not self-healing — if a Pod is deleted it is gone

- ReplicaSet:
  - Ensures a fixed number of Pods are always running
  - If a Pod dies, RS creates a new one
  - Does not support rolling updates or rollback directly

- Deployment:
  - High-level controller used in production
  - Manages ReplicaSets
  - Adds rolling updates, rollback, pause/resume, versioning
  - Recommended for stateless apps

### Relationship

Deployment → creates and owns ReplicaSet → ReplicaSet creates and maintains Pods → Pods run containers

### Basic Deployment YAML structure

```yaml
apiVersion: apps/v1
kind: Deployment
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

Note: Deployment YAML looks almost identical to ReplicaSet YAML; the difference is the `kind` and Deployment manages RS lifecycle.

### What happens when you apply a Deployment

- `kubectl apply -f deployment.yaml` → API Server validates and stores object in etcd
- Deployment controller wakes up and creates or updates a ReplicaSet
- ReplicaSet creates required Pods
- Scheduler assigns Pods to nodes
- kubelet pulls images and starts containers

### Internal control plane flow

- kubectl: sends YAML
- API Server: validates and stores state
- etcd: stores everything
- Deployment Controller: manages ReplicaSets
- ReplicaSet Controller: manages Pods
- Scheduler: assigns nodes
- Kubelet: runs containers on node
- Container runtime: actually runs container

### How self-healing works

- Kubernetes runs a reconciliation loop: desired state is stored in etcd; actual state comes from kubelets; if mismatch is found, controllers fix it.

Example: desired replicas = 3, actual running = 2 → system creates 1 new Pod

### Rollout strategies

- RollingUpdate (default): gradually replace old Pods with new ones. Uses `maxUnavailable` and `maxSurge` to control pace and availability.
- Recreate: delete all old Pods first, then create new Pods. Useful when two versions cannot run together.

### How updates work internally

- New Pod template hash is generated when spec changes
- Deployment creates a new ReplicaSet for the new template
- New Pods are created gradually while the old RS is scaled down
- Old ReplicaSet is kept for rollback purposes

### Rollback concept

- Deployment keeps revision history via ReplicaSets; rollback applies by scaling down the current RS and scaling up an older RS.

Useful commands:

- `kubectl rollout status deployment/<name>`
- `kubectl rollout history deployment/<name>`
- `kubectl rollout undo deployment/<name>`

### Pausing and Resuming rollout

- Pause: `kubectl rollout pause deployment/<name>`
- Resume: `kubectl rollout resume deployment/<name>`

### Scaling a Deployment

- `kubectl scale deployment <name> --replicas=10` — updates the Deployment spec and ReplicaSet adjusts pods accordingly.

### How data is stored in etcd (conceptual)

- `/registry/deployments/<namespace>/<name>`
- `/registry/replicasets/<namespace>/<name>`
- `/registry/pods/<namespace>/<name>`

Deployment stores: spec, strategy, revision, template, status
ReplicaSet stores: desired replicas, template hash, owner reference to Deployment
Pod stores: spec, nodeName, status

### Deleting a Deployment

- `kubectl delete deployment <name>` deletes the Deployment, its ReplicaSets, and Pods; entries are removed from etcd.

### One line summary

- Deployment is a high-level controller that manages ReplicaSets and Pods, provides rolling updates, rollback, scaling, and continuously maintains the desired state using the reconciliation loop.

### Quick exam / interview lines

- Pod is not self-healing, ReplicaSet and Deployment provide self-healing.
- Deployment is the recommended way to run stateless applications.
- Every update creates a new ReplicaSet.
- Rollback is just switching back to an old ReplicaSet.
- Kubernetes always works on desired state vs actual state.
