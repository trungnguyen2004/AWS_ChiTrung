---
title: "Difficulties Encountered & Future Roadmap"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.6.4. </b> "
---

In this final section, review technical engineering challenges encountered during cloud deployment, root causes, implemented solutions, and future cloud architectural enhancements.

---

### 1. Technical Engineering Challenges & Solutions

#### Challenge 1.1: Windows vs. Linux Path Incompatibility in React Router Builds
- **Root Cause:** Executing `npm run build` locally on a Windows machine generates server bundles containing Windows backslash path separators (`build\client`). When deployed into Linux Docker containers on EC2, `@react-router/serve` fails to resolve asset modules, throwing HTTP 404 errors for all JS and CSS bundles.
- **Implemented Solution:** Offloaded the entire build process to GitHub Actions Linux runners (`ubuntu-latest`). Building Docker images on Linux ensures standard forward-slash `/` path resolution. Rule: *Frontend Docker images are never compiled locally on Windows*.

#### Challenge 1.2: EC2 Free Tier Memory Constraints & Out-of-Memory (OOM) Crashes
- **Root Cause:** Running both the Java 21 Spring Boot JVM and React Node.js server concurrently on a `t2.micro` (1 GB RAM) instance triggered Linux OOM killer events, abruptly killing backend processes.
- **Implemented Solution:** Configured explicit JVM memory caps in `docker-compose.yml`:
  ```yaml
  JAVA_TOOL_OPTIONS: "-Xms256m -Xmx512m"
  ```
  Restricting JVM heap to 512 MB preserves sufficient RAM for Node.js and Linux system processes.

#### Challenge 1.3: CloudFront Distribution Domain Verification Requirement
- **Root Cause:** Creating CloudFront Distributions was blocked due to AWS account verification requirements (*"Your account must be verified before you can add new CloudFront resources"*).
- **Implemented Solution:** Completed all prerequisite ACM SSL certificate issuance (`us-east-1`) and Route 53 DNS validation. Routed public web traffic directly to EC2 via Route 53 A Record until AWS Support completes verification.

---

### 2. Future Architectural Development Roadmap

#### Roadmap 2.1: Infrastructure as Code (AWS CDK v2)
- Replace manual AWS Console resource creation with TypeScript AWS CDK v2 scripts.
- Single command deployment (`cdk deploy`) to spin up identical staging and production environments.

#### Roadmap 2.2: Multi-Region Active-Active High Availability
- Provision EC2 instances in `ap-southeast-1` (Singapore) and `us-east-2` (Ohio).
- Leverage Amazon ECR Cross-Region Replication rules and Route 53 Latency-Based Routing for automatic geographically nearest server routing.

#### Roadmap 2.3: Amazon Aurora Global Database Migration
- Migrate from RDS PostgreSQL to Amazon Aurora PostgreSQL using AWS Database Migration Service (DMS).
- Enable Aurora Global Database with Sydney as Primary and Singapore/Ohio as read replicas.

#### Roadmap 2.4: Auto Scaling & Load Balancing (ASG + ALB)
- Replace single EC2 instance with an Auto Scaling Group (ASG) behind an Application Load Balancer (ALB).
- Scale compute instances dynamically based on CPU utilization and incoming traffic spikes.

#### Roadmap 2.5: Full CloudWatch Observability & Automated Alarms
- Install **CloudWatch Agent** on EC2 to collect RAM and Disk Space metrics.
- Configure **Route 53 Health Checks** to monitor `https://trackermaint.dpdns.org` availability.
- Set up **CloudWatch Alarms + Amazon SNS** email alerts for High CPU (>80%) and disk exhaustion.