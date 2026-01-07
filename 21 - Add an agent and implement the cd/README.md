# Add an agent and implement the CD

Short summary:

Add a deployment agent (runner) and implement continuous delivery for deployments.

What you'll learn:


Resources:


Exercises:


Notes:


## CI/CD with Kubernetes — Personal Class Revision Notes

### Big picture

This class was mainly about building CI/CD pipelines and deploying to Kubernetes using VMs and GitHub Actions.

Main focus:

- CI (Continuous Integration)
- CD (Continuous Delivery / Deployment)
- Kubernetes deployment
- Multi-stage testing environments

### What is CI/CD?

Continuous Integration (CI)

- Developers push code frequently

Pipeline automatically:

	- Builds code
	- Runs tests
	- Runs scans

Goal: Keep main branch always deployable

Continuous Deployment (CD)
- Extends CI
- Automatically deploys app to environment (or prod)
- No downtime deployment

Difference:

- CI = build + test
- CD = deploy

### Environment setup

Using Virtual Machines

- Kubernetes cluster is running on VM

Tools used: VirtualBox, Vagrant

Vagrant helps:

	- Create VM using script
	- Preconfigure Kubernetes
	- Use prebuilt images (avoid manual install)

### GitHub Actions in this setup

Used as CI/CD engine. Can use GitHub-hosted runners or self-hosted runners (custom runners).

### Custom GitHub Runners (important)

Instead of GitHub VM, we used our own machine.

Typical steps:

1. Create VM (Linux preferred)
2. Install runner
3. Run: `./config.sh` and register with GitHub repo
4. Add labels & security config
5. Start runner

Now GitHub sends jobs to YOUR VM.

### Artifact flow (from your diagram)

Artifactory → SIT → Performance Testing → Security Testing → UAT/Prod

### Testing stages explained

1. SIT (System Integration Testing)
	 - Deploy app
	 - Run integration tests
	 - Validate all services work together
2. Performance Testing
	 - Deploy app
	 - Run load and stress tests
	 - Check speed and stability
3. Security Testing
	 - Deploy app in security test env
	 - Run DAST scans to find runtime vulnerabilities
4. UAT (User Acceptance Testing)
	 - Business validates app
	 - Final signoff before production

### Where artifacts come from

Docker images / build outputs stored in: Nexus, Artifactory, ECR / ACR

### How this pipeline works

Code Push
	↓
CI (Build + Test + Scan)
	↓
Build Artifact / Docker Image
	↓
Store in Artifact Repo
	↓
Deploy to SIT → Test
	↓
Deploy to Performance → Test
	↓
Deploy to Security → Test
	↓
Deploy to UAT / Prod

### Important class notes

- CI and CD are usually separate pipelines
- CD is more dangerous → needs approvals sometimes
- Custom runners are used when:
	- Need access to private network
	- Need Kubernetes access
	- Need special tools

### My mental model

- CI = factory
- Artifact repo = warehouse
- CD = delivery pipeline
- SIT/Perf/Security = quality gates
- Prod = final destination

### One line summary

This class was about building a full CI/CD pipeline using GitHub Actions and Kubernetes with multi-stage testing environments running on VMs.
