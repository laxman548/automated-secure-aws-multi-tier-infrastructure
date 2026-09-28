# Automated Secure AWS Multi-Tier Infrastructure

A hands-on Cloud and DevOps project focused on designing, deploying, securing, and automating a real-world multi-tier application infrastructure on AWS.

## Project Objective

Design and deploy a secure AWS infrastructure where:

- Users can access the application from the internet.
- Public access is controlled through a load balancer.
- Application servers remain inside private subnets.
- Sensitive data remains inside a private database.
- Infrastructure is gradually automated using Terraform.
- Deployment and operational tasks are automated using DevOps tools.

The project is being built step by step, starting with AWS networking and gradually introducing EC2, load balancing, Docker, Terraform, CI/CD, monitoring, and Kubernetes.

## Planned Architecture

```text
                         INTERNET
                             |
                             v
                  +----------------------+
                  |   Public Load        |
                  |      Balancer        |
                  +----------+-----------+
                             |
                   +---------+---------+
                   |                   |
                   v                   v
          +----------------+   +----------------+
          | App Server 1   |   | App Server 2   |
          |    PRIVATE     |   |    PRIVATE     |
          +-------+--------+   +-------+--------+
                  |                    |
                  +---------+----------+
                            |
                            v
                   +------------------+
                   |     Database     |
                   |     PRIVATE      |
                   +------------------+
```

### Network Principle

> Public access reaches the controlled entry point, while application servers and sensitive data remain private.

## Network Design

The project will use a dedicated AWS VPC.

| Component | CIDR |
|---|---|
| VPC | `10.50.0.0/16` |
| Public Subnet A | `10.50.1.0/24` |
| Public Subnet B | `10.50.2.0/24` |
| Private Subnet A | `10.50.11.0/24` |
| Private Subnet B | `10.50.12.0/24` |

### Planned Network Flow

```text
Internet
   |
   v
Internet Gateway
   |
   v
Public Subnets
   |
   v
Load Balancer
   |
   v
Private Application Subnets
   |
   v
Private Database
```

Private application servers may use a NAT Gateway for required outbound internet access without receiving direct inbound internet connections.

## Security Approach

The project will follow basic cloud security principles:

- Separate public and private subnets
- No public IP addresses on private application servers
- Security Groups to control network traffic
- IAM for AWS access control
- Private database tier
- Secure management access
- Secrets excluded from Git
- Terraform state excluded from Git
- Least-privilege permissions where practical
- Monitoring and logging through AWS services

## Technologies

### Cloud

- AWS
- VPC
- EC2
- Elastic Load Balancing
- IAM
- CloudWatch

### DevOps

- Git
- GitHub
- GitHub Actions
- Docker
- Terraform

### Operating System and Scripting

- Linux
- Bash
- AWS CLI

### Future

- Kubernetes
- Amazon EKS

## Project Phases

### Phase 1 — Project Setup

- GitHub repository
- Git
- Documentation
- `.gitignore`

### Phase 2 — AWS Setup

- AWS CLI
- IAM
- AWS authentication

### Phase 3 — Networking

- VPC
- Availability Zones
- Public subnets
- Private subnets
- Internet Gateway
- Route Tables
- NAT Gateway

### Phase 4 — Compute

- EC2
- Linux application servers
- Private server deployment
- Connectivity testing

### Phase 5 — Security

- Security Groups
- IAM roles
- Secure server access
- Network security validation

### Phase 6 — Load Balancing

- Application Load Balancer
- Target Groups
- Listeners
- Health Checks
- Traffic distribution

### Phase 7 — Containers

- Docker
- Dockerfile
- Containerized application
- Application deployment

### Phase 8 — Infrastructure as Code

- Terraform
- Providers
- Resources
- Variables
- Outputs
- State management
- Infrastructure automation

### Phase 9 — CI/CD

- GitHub Actions
- Automated validation
- Automated deployment

### Phase 10 — Monitoring

- CloudWatch
- Logs
- Metrics
- Health monitoring

### Phase 11 — Kubernetes

- Kubernetes fundamentals
- Deployments
- Services
- Ingress
- Amazon EKS

## Learning Approach

This project is being developed progressively.

```text
Understand
    |
    v
Design
    |
    v
Implement
    |
    v
Test
    |
    v
Document
    |
    v
Commit to Git
    |
    v
Push to GitHub
```

Each technology will be learned before it is implemented.

## Planned Repository Structure

```text
automated-secure-aws-multi-tier-infrastructure/
|
+-- README.md
+-- .gitignore
|
+-- docs/
|   +-- 01-project-overview.md
|   +-- 02-architecture.md
|   +-- 03-aws-cli-setup.md
|   +-- 04-iam-setup.md
|   +-- 05-vpc-design.md
|   +-- 06-deployment-log.md
|
+-- scripts/
+-- config/
+-- diagrams/
|
+-- terraform/
+-- docker/
+-- k8s/
|
+-- .github/
    +-- workflows/
```

Additional directories will be added as the corresponding technologies are introduced.

## Current Status

**Phase 1 — Project Initialization**

- [x] GitHub repository created
- [x] Local Git repository initialized
- [x] `.gitignore` created
- [x] Project README created
- [ ] AWS CLI documentation
- [ ] IAM configuration
- [ ] VPC deployment
- [ ] EC2 deployment
- [ ] Security configuration
- [ ] Load Balancer
- [ ] Docker
- [ ] Terraform
- [ ] CI/CD
- [ ] Monitoring
- [ ] Kubernetes

## Final Goal

Build a working, secure, automated AWS infrastructure that demonstrates practical knowledge of:

**Cloud → Linux → Networking → Security → EC2 → Load Balancing → Docker → Terraform → CI/CD → Monitoring → Kubernetes**

## Project Status

This project is currently under development.