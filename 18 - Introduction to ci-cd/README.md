# Introduction to CI/CD

Short summary:

Principles and building blocks of Continuous Integration and Continuous Delivery.

What you'll learn:

- CI vs CD, pipelines, artifacts
- Typical stages: build, test, deploy
- Tooling examples: GitHub Actions, GitLab CI, Jenkins

Resources:

- Add links or notes here

Exercises:

- Design a simple CI/CD pipeline for a sample app

Notes:

- Add instructor notes or references here

---

## Introduction to CI/CD — Big Picture

1. Traditional Software Model (Before 2000)

   Waterfall Model

   Flow:

   RA → Design → Coding → Testing

   Problems:

   - Very slow feedback cycle
   - Changes are costly
   - Bugs are found very late
   - Not suitable for fast business requirements

2. Agile Model

   Agile = doing things quickly and easily with fast feedback

   - Small iterations
   - Frequent releases
   - Continuous feedback
   - Developers integrate code frequently

3. CI (Continuous Integration)

   CI is mainly driven by developers

   Typical CI Pipeline:

   GitHub → Linting → Dependency Check → SCA → SAST → Package (jar/war/exe)

   Where:

   - Linting = code quality checks
   - Dependency Check = vulnerable libraries detection
   - SCA = Software Composition Analysis
   - SAST = Static Application Security Testing

4. Unit Testing

   - Happens before packaging
   - Tests individual modules/functions
   - Ensures business logic correctness
   - Mandatory before building Docker images

5. Docker Image Build & Testing Flow

   Unit Testing → Build Docker Image → Test Image using Container → Trivy Scan → Push to Image Repo

6. Image Artifact Repositories

   Used to store tested and approved images:

   - Nexus
   - AWS ECR
   - Azure ACR
   - DockerHub

7. Continuous Delivery vs Continuous Deployment

   Continuous Delivery

   - Pipeline stops before production
   - Manual approval is required

   Continuous Deployment

   - Automatically deploys to production
   - No manual approval gate

8. Testing Stages After Deployment

   8.1 SIT — System Integration Testing

   - Runs after deployment
   - Executes integration and system test cases

   8.2 Security Testing

   - Uses DAST (Dynamic Application Security Testing)
   - Flow: Deploy → Run → Scan → Fix

   8.3 Performance Testing

   - Tests load, stress, stability, response time

   8.4 UAT — User Acceptance Testing

   - Done by business/users
   - Final validation before production

9. Complete DevOps Pipeline

   Code → CI → Unit Test → Build → Docker Image → Scan → Store → Deploy → SIT → Security → Performance → UAT → Production

10. Tools Mentioned

- GitHub
- Docker
- Trivy
- Nexus / ECR / ACR / DockerHub

## Final Mental Model

DevOps = Fast development + Automated testing + Security scanning + Reliable deployment

One Line Summary

DevOps automates the entire software lifecycle from code to production with continuous testing, scanning, and deployment.
