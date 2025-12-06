# Infrastructure Provisioning & Self-Hosted GitHub Runner

## Overview

This repository contains the infrastructure code and configuration required to:

- Deploy a **Self-Hosted GitHub Runner** (VM / EC2)
- A **Terraform backend stored in an AWS S3 bucket**
- IAM roles and permissions required for infrastructure automation  
- Provision an **Amazon EKS Kubernetes Cluster** using Terraform
- Prepare the environment for the CI/CD pipeline executed in Repo 2
- Support advanced runtime monitoring tools (Falco) and scanners (Trivy, SonarQube)
   

This repository represents **Repo 1** of the project and forms the foundation that Repo 2 builds on for full CI/CD, application deployment, and security automation.

---

## Architecture Diagram

The following draw.io workflow illustrates the infrastructure setup, runner provisioning, and interaction between Repo 1 and Repo 2:

<img width="3441" height="1489" alt="image" src="https://github.com/user-attachments/assets/49a46064-9188-4725-bc62-f0c79b337da9" />

This diagram shows:
- GitHub Runner deployment   
- EKS provisioning workflow  
- How Repo 2 reuses the same runner to deploy workloads  

---

## Architecture Summary

1. A GitHub Runner EC2 instance is deployed.
2. The runner is configured with:
   - Docker
   - kubectl
   - Terraform
   - Falco agent
   - SonarQube scanner
   - Trivy
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
- Install Falco and SonarQube scanner

### 2. Clone Repo 1 onto the Runner
Provides Terraform files and provisioning scripts.

### 3. Run Terraform
```bash
terraform init
terraform plan
terraform apply
