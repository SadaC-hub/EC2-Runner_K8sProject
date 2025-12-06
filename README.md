# Infrastructure Provisioning & Self-Hosted GitHub Runner

## Overview

This repository contains the infrastructure code and configuration required to:

- Deploy a **Self-Hosted GitHub Runner** (VM / EC2)
- Provision an **Amazon EKS Kubernetes Cluster** using Terraform
- Prepare the environment for the CI/CD pipeline executed in Repo 2
- Support advanced runtime monitoring tools (Falco) and scanners (Trivy, SonarQube)

This repo is responsible for the **build foundation** of the Kubernetes CI/CD project.

---

## Architecture Summary

1. A GitHub Runner EC2 instance is deployed.
2. The runner is configured with:
   - Docker
   - kubectl
   - Terraform
   - Optional: Falco agent, SonarQube scanner, Trivy
3. Terraform code provisions:
   - EKS Cluster
   - Worker nodes
   - IAM roles and permissions
4. The same runner instance is used later by Repo 2 to:
   - Scan code
   - Build Docker images
   - Deploy to EKS

---

## Why a Self-Hosted Runner?

- Supports **custom tooling** (Falco, Trivy FS, SonarQube scanner).
- Allows infrastructure provisioning directly from GitHub Actions.
- Enables full customisation of CI/CD behaviour.
- Suitable for projects that require **privileged operations**, such as:
  - Terraform apply  
  - Building & pushing Docker images  
  - Installing kernel-level tools (Falco)

---

## Tools Used

| Tool | Purpose |
|------|---------|
| **Terraform** | Provisions Amazon EKS cluster and networking components |
| **Amazon EKS** | Managed Kubernetes platform |
| **GitHub Self-Hosted Runner** | Executes CI/CD and scanning workflows |
| **AWS IAM** | Permissions for provisioning and cluster access |
| **kubectl** | Manages deployments to the Kubernetes cluster |

---

## Workflow

### 1. Build the Runner Instance
- Deploy EC2 instance
- Install GitHub Actions Runner service
- Install Docker, Terraform, kubectl
- (Optional) Install Falco and SonarQube scanner

### 2. Clone Repo 1 onto the Runner
Provides Terraform files and provisioning scripts.

### 3. Run Terraform
```bash
terraform init
terraform apply
