
# Docker Networking

Short summary:

Container networking concepts and practical commands.

What you'll learn:

- Bridge, host, overlay networks
- Exposing ports and linking containers
- DNS and service discovery basics

Resources:

- Add links or notes here

Exercises:

- Create a network of multiple containers communicating together

Notes:

- Add instructor notes or references here

---

## Docker Networking — Personal Notes (expanded)

Below are concise, slightly elaborated notes to help you remember core Docker networking concepts.

### First, core networking reminders

- IP address = unique address of a machine
- NIC = physical network interface card
- Switch = forwards Ethernet frames using a MAC table (inside a LAN)
- ARP = maps IP → MAC
- Gateway = exit point of a subnet

NAT variants:

- SNAT = change source IP (commonly used for outbound internet access)
- DNAT = change destination IP (commonly used for port forwarding)

### Why Docker networking exists

Containers must be able to:


Docker implements this by combining Linux primitives: network namespaces, veth pairs, bridges and iptables NAT rules.

### Network namespace (very important)




	- Network interfaces
	- Routing table
	- Netfilter (firewall) rules

This makes each container believe it has its own private network stack.

### veth pair (virtual cable)

- A veth pair is two linked virtual interfaces. One end is placed inside the container (commonly eth0) and the other lives on the host (vethXXXX).
- Packets entering one side appear on the other — think of it as a virtual cable connecting container to the host bridge.

### docker0 bridge (default)

- Docker creates a default bridge (`docker0`) which acts like a virtual switch.
- Containers attached to this bridge receive addresses (e.g. 172.17.x.x) and can communicate with each other directly.

Typical flow for outbound traffic:

Container → eth0 (in namespace) → veth → docker0 bridge → host network stack → NAT → internet

### How containers reach the internet (NAT)

- Docker typically adds an iptables MASQUERADE rule for outbound traffic. The container's source IP is replaced with the host IP. Returning packets arrive at the host and are forwarded back to the container.

### Port mapping (-p)

Example: `docker run -p 8080:80 nginx`

- This creates DNAT rules so traffic arriving at Host:8080 is forwarded to Container:80. iptables do the heavy lifting (DNAT + connection tracking).

### Types of Docker networks (short list)

1. bridge — default, host-local L2 bridge, common for single-host apps
2. host — container shares the host network namespace (no isolation)
3. none — container has no network interfaces
4. overlay — multi-host networking (used by Swarm, Kubernetes uses other CNI plugins)
5. macvlan — gives containers real MACs on the LAN (useful for DHCP/broadcast-aware services)

#### Bridge network

- Default and most common. Containers on the same bridge can talk to each other. Use `docker network create` to make custom bridges (they provide DNS and better isolation).

#### Host network

- `--network host` makes the container use the host network directly — low overhead but no network isolation.

#### None

- No network interfaces — used for completely isolated tasks.

#### Overlay

- Enables cross-host container communication (VXLAN under the hood in many implementations). Used for orchestration (Swarm, Kubernetes CNI alternatives).

#### Macvlan

- Attaches a container directly to the physical network with its own MAC. Useful in special cases where containers must appear as first-class hosts on the LAN.

### Inspecting Docker networks and containers

- `docker network ls` — list networks
- `docker network inspect <name>` — see configuration and connected containers
- `docker inspect <container>` — view container networking details (IP, MAC, network namespace)

### Default bridge vs custom bridge

- Default bridge: no embedded DNS, must use IPs
- Custom bridge: embedded DNS, containers can resolve each other by name and it's cleaner for app stacks

### Real-world pattern (app + DB)

1. Create a custom network: `docker network create mynet`
2. Start app and db attached to `mynet`
3. Use the service name (e.g., `db:3306`) inside the app; built-in DNS resolves container names

### Final mental model

- Each container owns its own network namespace
- Connectivity is achieved by connecting the namespace to a bridge via a veth pair
- Internet access is typically granted via NAT on the host
- Recommendation: use custom bridge networks for day-to-day app stacks

### One-line summary

Docker networking = Linux networking primitives (namespaces + veth + bridges) + iptables/NAT magic.

