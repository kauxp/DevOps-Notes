# Intoduction to Kubernetes

Short summary:

Kubernetes fundamentals: why it exists and core concepts.

What you'll learn:


Resources:


Exercises:


Notes:


## Kubernetes — Personal Revision Notes (expanded)

What is Kubernetes?

Kubernetes (K8s) is a container orchestration platform — think of it as an operating system for your data center. It automates deploy, scale, heal, networking and storage, and supports rolling updates. The key idea is declarative: you declare the desired state, and Kubernetes continuously works to make reality match that declaration.

### Big picture architecture

Two main parts:

- Control Plane — the brain that makes scheduling and cluster decisions
- Worker Nodes — the machines that actually run your containers (the muscles)

#### Control Plane components

- kube-apiserver — the API front-end. All kubectl requests go here. It validates requests and talks to etcd. If the API server is down, the cluster cannot be controlled.

- etcd — a distributed key-value store that holds cluster state, config, and critical data. If etcd is lost without a backup, the cluster state is unrecoverable.

- kube-scheduler — decides which node a Pod should run on by evaluating resource requests (CPU, memory), node health, and policy constraints.

- kube-controller-manager — runs controllers that compare desired state vs actual state and perform fixes (create/delete pods, maintain replicas, manage endpoints).

- cloud-controller-manager — runs cloud-provider specific controllers (only used in cloud environments) to manage load balancers, disks, and routes.

#### Worker Node components

- kubelet — runs on every node. It talks to the API server and makes sure the containers described by Pod specs are running. Think of kubelet as the hands of Kubernetes: it pulls images, starts containers, and restarts them when necessary.

- kube-proxy — implements Service network rules on the node to provide cluster networking and load balancing for Services.

- Container runtime — containerd or CRI-O (the component that actually runs containers).

- CNI (Container Networking Interface) — provides pod networking (examples: Calico, Flannel, Cilium).

- CSI (Container Storage Interface) — provides persistent storage for volumes.

### What happens when you apply YAML

1. You run `kubectl apply -f <file>` which sends the request to the kube-apiserver.
2. API server validates and stores the object in etcd.
3. Scheduler assigns the pod to a node based on constraints.
4. kubelet on the chosen node creates the container(s) using the runtime.
5. kube-proxy and the network stack update routing/rules so the pod can be reached.
6. Controllers continuously watch and reconcile replicas and other resources.

### Mental model (short)

- Brain = control plane
- Memory = etcd
- Hands = kubelet
- Traffic police = kube-proxy
- Decision maker = kube-scheduler
- Auto-fixer = controllers

One-line summary

Kubernetes continuously works to make reality match the desired state you declare.
