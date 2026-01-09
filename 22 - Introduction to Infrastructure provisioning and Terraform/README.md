# Introduction to Infrastructure provisioning and Terraform

Short summary:

Fundamentals of infrastructure-as-code using Terraform.

What you'll learn:

- Terraform concepts: providers, resources, state
- Writing modules and managing environments
- Best practices for state and remote backends

Resources:

- Add links or notes here

Exercises:

- Write simple Terraform to provision a VM or network resource

Notes:

- Add instructor notes or references here

## Terraform & Infrastructure as Code — Personal Notes

Overview

Infrastructure as Code (IaC) means managing infrastructure using code instead of manual steps. Benefits include repeatability, version control, auditability, speed, and reduced human error.

Categories of IaC tools

### Infrastructure provisioning tools

Used to create servers, networks, and load balancers.

Examples: AWS CloudFormation, Azure Bicep, Terraform (platform-agnostic)

### Configuration management tools

Used to install packages, configure software, and maintain state. These tools help prevent configuration drift.

Examples: Ansible, Puppet, Chef

### Server templating tools

Used to create pre-baked machines or images. Examples: Vagrant, Docker

Why Terraform?

- Platform-agnostic: works with AWS, Azure, GCP, Kubernetes
- Declarative language
- Keeps state in the `terraform.tfstate` file

Terraform state file

The `terraform.tfstate` file is the source of truth. It records what Terraform has created and what exists in the cloud. Terraform uses it to detect changes and compute a plan.

Terraform lifecycle commands

- `terraform init` — Initialize the working directory, download providers, and configure the backend.
- `terraform plan` — Preview what will be created, changed, or destroyed (safe preview).
- `terraform apply` — Execute changes (create/update/delete resources).
- `terraform destroy` — Remove resources created by Terraform.

Typical workflow

1. Write `.tf` files
2. `terraform init`
3. `terraform plan`
4. `terraform apply`

Example use case: create an EC2 instance

- Write EC2 configuration in a `.tf` file
- Run `init` → `plan` → `apply`
- Terraform creates the VM and stores state in `terraform.tfstate`

Configuration drift

When actual infrastructure differs from the expected configuration. Terraform compares the state file with real infrastructure and shows differences in the plan.

How Terraform fits into DevOps

- CI pipeline: run `terraform plan` as a gated review step and optionally `terraform apply` in a controlled environment.
- CD pipeline: automated environment creation and managed infra updates.

My mental model

- Terraform = infrastructure factory
- `.tf` files = blueprints
- `terraform.tfstate` = memory
- plan = preview
- apply = execute

One-line summary

Terraform is a platform-agnostic Infrastructure as Code tool that creates, updates, and manages cloud resources using code and state tracking.

## AWS + Terraform — Big Picture

This class focused on building AWS infrastructure using Terraform and understanding how networking, state, and dependencies work together.

### 1. VPC Basics

What is a VPC?

VPC = Virtual Private Cloud

It is your own private network inside AWS. You define:

- IP range (CIDR)
- Subnets
- Routing
- Internet access

### 2. CIDR Block

CIDR defines how big your network is.

Example: `10.0.0.0/16`

Means:

- Large private network range
- You split this into subnets

### 3. Subnets

Subnet = smaller network inside VPC

Types:

- Public Subnet

  - Has route to Internet Gateway
  - Can access internet
  - Used for: load balancers, bastion hosts, public servers

- Private Subnet

  - No direct internet access
  - Used for: databases, backend services

### 4. Route Tables

Route tables decide where traffic goes.

Example:

`0.0.0.0/0` → Internet Gateway = public subnet

No IGW route = private subnet

### 5. Types of IP Addresses in AWS

- Private IP — Used inside VPC only; not reachable from internet
- Public IP — Reachable from internet; may change if instance restarts
- Elastic IP — Static public IP; can be reattached to other instances

### 6. EC2 and AMI

You cannot create an EC2 instance without an AMI.

AMI = OS image (Ubuntu, Amazon Linux, etc). AMI is the base for EC2.

### 7. Terraform State File

`terraform.tfstate` = memory of Terraform

Stores:

- What exists in cloud
- What Terraform created

Used for:

- Change detection
- Dependency tracking

### 8. Remote State (Team Work)

When many people work together:

Store state in:

- S3

Lock using:

- DynamoDB

Benefits:

- No state conflicts
- Safe collaboration

### 9. Terraform File Structure (Common Practice)

- `main.tf` → main logic
- `network.tf` → VPC, subnets, IGW
- `compute.tf` → EC2, security groups
- `variables.tf` → inputs
- `outputs.tf` → outputs

### 10. Terraform Dependencies

Terraform figures out order automatically. This is called implicit dependency.

Example:

Subnet uses VPC ID → Terraform creates VPC first

### 11. Variables in Terraform

- Input variables — make code reusable
- Output variables — show useful values after apply
- Local variables — used inside file only

### 12. My Mental Model

- VPC = your data center
- Subnet = your rack
- Route table = traffic police
- Terraform = infra factory
- tfstate = memory

One Line Summary

This class teaches how to design AWS networking and infrastructure using Terraform with proper state management, variables, and dependency handling.
