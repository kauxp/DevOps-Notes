# Deep dive into Kubernetes services

Short summary:

Advanced concepts around services, ingress, and service mesh basics.

What you'll learn:

- Ingress controllers and routing
- Headless services and DNS
- Introduction to service meshes (Istio, Linkerd)

Resources:

- Add links or notes here

Exercises:

- Configure an Ingress to route traffic to multiple services

Notes:

- Add instructor notes or references here

---

## Networking — Big Picture

Kubernetes networking must solve:

- Container-to-Container (inside Pod)
- Pod-to-Pod (same node)
- Pod-to-Pod (different nodes)
- Pod-to-Service
- Pod-to-Internet (egress)
- Internet-to-Pod (ingress)

Design rules (Kubernetes model):

- All Pods can talk to all Pods WITHOUT NAT
- All Nodes can talk to all Pods WITHOUT NAT
- Pod IP is same inside and outside the pod

### 1. Container-to-Container (inside a Pod)

All containers in a Pod:

- Share the same network namespace
- Same IP
- Same ports
- Same loopback

So: containers talk using localhost.

My mental model: A Pod is one machine, containers are processes.

### 2. Pod-to-Pod (same node)

Each Pod has its own network namespace. Pods on the same node are connected using a veth pair and a Linux bridge.

Flow example:

Pod eth0 → veth → Linux bridge → veth → other Pod eth0

The bridge uses ARP to find the destination Pod on the same node.

### 3. Pod-to-Pod (different nodes)

Each node is assigned a Pod CIDR range (example):

- Node1: 10.0.1.0/24
- Node2: 10.0.2.0/24

Flow example:

Pod → veth → bridge

Bridge cannot ARP for off-node IP → sends to node eth0

Node routes packet to other node

Destination node → bridge → veth → target Pod

Who manages cross-node routing? The CNI plugin (Calico, Flannel, Weave, AWS VPC CNI, etc.).

### 4. Pod-to-Service (ClusterIP)

Problem: Pods are ephemeral — IPs change and pods scale. Solution: Service provides a stable IP + DNS name.

How traffic is routed (two common implementations):

- iptables: kube-proxy writes iptables rules. Traffic to ServiceIP is DNAT'ed to a selected Pod; return traffic may be SNAT'ed back.
- IPVS: kernel-level load balancer used by kube-proxy for higher performance and scalability.

### 5. DNS (CoreDNS)

Every Service gets a DNS name: `service.namespace.svc.cluster.local`.

CoreDNS runs in-cluster and watches Services and Endpoints so Pods can use names (for example `http://backend-svc`) instead of IPs.

### 6. Pod-to-Internet (Egress)

Problem: Internet won’t know Pod IPs. Solution: Nodes perform SNAT for outbound traffic.

Flow: Pod IP → Node IP (SNAT) → Internet.

### 7. Internet-to-Pod (Ingress)

Two common options:

- Service Type: LoadBalancer (L4) — Internet → Cloud LB → NodePort → Service → Pod. Good for simple TCP/UDP workloads.
- Ingress Controller (L7) — examples: NGINX Ingress, AWS ALB, Traefik, Istio Gateway. Provides host/path routing and TLS termination: Internet → Ingress → Service → Pod.

### How this matches class notes

- Containers in same Pod use localhost
- Pods on same node use veth + bridge (Linux networking)
- Pods across nodes use routing managed by the CNI
- kube-proxy maintains Service routing (iptables or IPVS)
- CoreDNS handles service name resolution
- Services abstract pods and provide load balancing

### Commands to observe networking

Use these kubectl commands to inspect services, endpoints and pod-to-node mappings:

```bash
kubectl get svc
kubectl describe svc <svc-name>
kubectl get endpoints
kubectl get pods -o wide
```

### Final mental model

- Pod = mini machine
- veth = virtual cable
- bridge = virtual switch
- CNI = network engineer
- Service = virtual load balancer
- kube-proxy = traffic police
- CoreDNS = phonebook

### One-page summary

- Container-to-Container → localhost
- Pod-to-Pod (same node) → veth + bridge
- Pod-to-Pod (cross node) → routing via CNI
- Pod-to-Service → iptables/IPVS DNAT
- DNS → CoreDNS
- Egress → SNAT Pod → Node → Internet
- Ingress → LoadBalancer or Ingress Controller

### One-line summary

Kubernetes networking is Linux networking + CNI + kube-proxy + CoreDNS working together to make the cluster look like one flat network.
