# Microservices Introduction

Short summary:

Introduce microservices architecture: principles, benefits, trade-offs, and common patterns.

What you'll learn:

- What microservices are and why use them
- Service boundaries and decomposition
- Communication patterns (REST, gRPC, messaging)
- Challenges: data consistency, observability, testing

Resources:

- Add links or notes here

Exercises:

- Add example system decomposition tasks here

Notes:

- Add instructor notes or references here

## Concept

A microservices architecture breaks a large monolithic application into smaller, independent services that communicate via APIs. Each service handles a specific business capability (e.g., authentication, payments, catalog, orders).

## Core Components

Component | Description
--- | ---
API Gateway | Single entry point for all backend microservices. Handles routing, authentication, rate limiting, caching, and protocol translation.
Service Registry / Discovery | Tracks active service instances for dynamic discovery (e.g., Consul, Eureka, etcd).
DNS | Maps service names to IPs; often integrated with service discovery.
Load Balancer | Distributes traffic for high availability & scalability (Nginx, AWS ELB).
Database per Service | Each service owns its database for loose coupling (SQL or NoSQL).
SQL Databases | Structured, transactional, relational data (PostgreSQL, MySQL).
NoSQL Databases | Flexible, horizontally scalable (MongoDB, DynamoDB, Cassandra).
Synchronous Communication | Request-response via HTTP/REST or gRPC; simple but adds latency.
Asynchronous Communication | Event-driven via Kafka, RabbitMQ, AWS SQS; decoupled and scalable.
Message Broker | Manages queues, pub/sub, and event streaming (Kafka, RabbitMQ).
Swagger / OpenAPI | API documentation and testing tools.
Event Sync / Event Bus | Enables event-driven workflows and eventual consistency.
Externalized Config & Logs | Centralized management with Spring Cloud Config, ELK Stack.
Monitoring / Tracing | Observability with Prometheus, Grafana, Jaeger, Zipkin.
Reporting / Analytics | Aggregates data for BI (ELK, Redshift, Snowflake).

## DNS (Domain Name System)

### Purpose (DNS)

Translates human-readable domain names (e.g., myapp.com) into IP addresses (e.g., 192.168.1.2).

### How it works

1. Client requests `myapp.com`.
2. Resolver queries → Root → TLD → Authoritative server.
3. Authoritative server returns IP address.
4. Browser connects to the IP.

### Cloud context

Managed via AWS Route 53, Azure DNS, Cloudflare; often used together with load balancing and failover.


## Load Balancers

### Purpose (Load Balancers)

Distributes requests to multiple servers for high availability, fault tolerance, and scalability.

### Types

Type | Operates On | Examples | Use Case
--- | --- | --- | ---
L4 (Transport) | TCP/UDP | AWS NLB, HAProxy | Fast, network-level routing
L7 (Application) | HTTP/HTTPS | AWS ALB, Nginx, Traefik | Smart routing that inspects headers & URLs

### Features

- Health checks
- Sticky sessions
- SSL termination
- Auto-scaling integration


## Synchronous vs Asynchronous Communication

Aspect | Synchronous | Asynchronous
--- | --- | ---
Definition | Caller waits for response | Caller sends message and continues
Example | REST API | Kafka, RabbitMQ
Protocol | HTTP/HTTPS | AMQP, MQTT
Use Case | Real-time (login) | Decoupled (notifications, billing)
Drawback | Tight coupling, higher latency | More complex to debug


## SQL vs NoSQL Databases

Feature | SQL (Relational) | NoSQL (Non-relational)
--- | --- | ---
Structure | Tables, rows | Key-value, Document, Graph
Schema | Fixed | Dynamic
Scaling | Vertical | Horizontal
Examples | MySQL, PostgreSQL | MongoDB, DynamoDB
Use Cases | Transactions | Scalable, flexible schema
Query Language | SQL | JSON-like / custom

Microservices often use both (polyglot persistence) depending on service needs.


## API Gateway

### Role

Sits between clients and backend microservices as a unified entry point.

### Responsibilities

- Routing: Direct requests to the proper service.
- Auth: Validate tokens (OAuth2, JWT).
- Rate limiting: Prevent overload.
- Request transformation: Modify headers, URLs.
- Caching: Improve performance.
- Monitoring: Centralized tracing and metrics.

### Popular Gateways

- AWS API Gateway
- Kong
- Nginx
- Istio
- Apigee


## Externalizing Logs

### Why

Container logs disappear after restarts — centralizing logs is essential for debugging and auditing.

### Common setup

Tool | Role
--- | ---
Fluentd / Fluent Bit / Logstash | Collect logs
Elasticsearch / Loki | Store & index logs
Kibana / Grafana | Visualize logs and metrics
Cloud-native | CloudWatch, Stackdriver, etc.

### Benefits

- Unified view across services
- Easier debugging and incident response
- Log-based alerts and analytics


## 12-Factor App Principles

The 12-factor methodology helps build portable, resilient cloud-native apps. High-level summary:

1. Codebase — One codebase, many deploys.
2. Dependencies — Explicitly declared.
3. Config — Store config in environment variables.
4. Backing services — Treat as attached resources.
5. Build, release, run — Separate stages.
6. Processes — Execute as stateless processes.
7. Port binding — Self-contained services expose ports.
8. Concurrency — Scale via process model.
9. Disposability — Fast startup and graceful shutdown.
10. Dev/prod parity — Keep environments similar.
11. Logs — Treat logs as event streams.
12. Admin processes — Run one-off admin/maintenance tasks.

These principles fit well with microservices, containers, and Kubernetes.
