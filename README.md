# 🔐 Infrastructure Provisioning & Self-Hosted GitHub Runner

<!-- Technology Badges -->
![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20EKS-orange?style=for-the-badge&logo=amazonaws&logoColor=white)
![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-blue?style=for-the-badge&logo=githubactions&logoColor=white)
![Runner](https://img.shields.io/badge/Runner-Self--Hosted-lightgrey?style=for-the-badge&logo=github&logoColor=white)
![DevSecOps](https://img.shields.io/badge/DevSecOps-Security%20Enabled-green?style=for-the-badge&logo=securityscorecard&logoColor=white)

<!-- Status Badges -->
![Project](https://img.shields.io/badge/Project-Foundation%20Repo%201-success?style=for-the-badge)

## Overview

This repository defines the **core infrastructure foundation** for a multi-repository DevSecOps Kubernetes project on **AWS**.

It is responsible for:

- Provisioning a secure **Amazon EKS cluster** using **Terraform (Infrastructure as Code)**
- Deploying a **self-hosted GitHub Actions runner on EC2** to support privileged CI/CD operations
- Configuring AWS **IAM roles and permissions** required for infrastructure automation and cluster access
- Preparing the execution environment for downstream CI/CD pipelines and application deployments
- Enabling integration with **security and runtime tooling** such as Falco, Trivy, and SonarQube

As **Repo 1**, this project establishes the shared infrastructure and execution layer that **[Repo 2](https://github.com/SadaC-hub/KubernetesProject)** builds upon to implement application delivery, security scanning, and automated deployment workflows.

---

## Architecture Diagram


```mermaid
flowchart LR
  Dev[Developer] --> Repo1[Repo 1 Infrastructure and Runner IaC]
  Dev --> Repo2[Repo 2 KubernetesProject]

  Repo1 -->|Terraform apply| AWS[AWS Account]
  AWS --> EC2[EC2 Self Hosted GitHub Runner]
  AWS --> S3[S3 Terraform Remote State]
  AWS --> IAM[IAM Roles and Policies]
  AWS --> EKS[EKS Cluster]

  Repo2 -->|GitHub Actions runs on| EC2

  EC2 -->|kubectl apply| EKS
  EKS --> Falco[Falco Runtime Monitoring DaemonSet]
```

```mermaid
flowchart TB
  Trigger[Push to main in Repo 2] --> GA[GitHub Actions Workflow]
  GA --> Runner[Self Hosted Runner on EC2]

  subgraph CI[CI Build and Scan]
    Runner --> Sonar[SonarQube Scan]
    Runner --> Deps[Install Dependencies]
    Runner --> TrivyFS[Trivy Filesystem Scan]
    Runner --> Build[Docker Build]
    Build --> TrivyImg[Trivy Image Scan]
    TrivyImg --> Push[Push Image to Docker Hub]
  end

  subgraph CD[CD Deploy to EKS]
    Runner --> Kubeconfig[Update kubeconfig for EKS]
    Kubeconfig --> Deploy[kubectl apply]
    Deploy --> EKS[EKS Cluster]
  end

  subgraph Runtime[Runtime Security and Alerting]
    EKS --> Falco[Falco DaemonSet]
    Falco --> Sidekick[Falcosidekick]
    Sidekick --> UI[Falcosidekick UI]
  end
```

### Architecture & Workflow Summary

- **Repo 1** provisions the shared cloud infrastructure using Terraform, including the Amazon EKS cluster and a self-hosted GitHub Actions runner on EC2.
- **Repo 2** contains the application and CI/CD logic and reuses the same self-hosted runner to execute build, scan, and deployment workflows.
- All CI and CD jobs run on the self-hosted runner, enabling privileged operations and the use of custom security tooling.
- Container images are built and scanned before being pushed to Docker Hub and deployed to EKS.
- Runtime security is enforced inside the cluster using Falco, with alerts forwarded via Falcosidekick and visualised in Falcosidekick UI.

---


## Tech Stack

| Category | Technologies |
|--------|--------------|
| Cloud Platform | AWS (EC2, EKS, IAM, S3) |
| Infrastructure as Code | Terraform |
| CI/CD | GitHub Actions (Self-Hosted Runner) |
| Containerisation | Docker |
| Kubernetes | Amazon EKS, kubectl |
| Security Scanning | Trivy, SonarQube |
| Runtime Security | Falco, Falcosidekick, Falcosidekick UI |
| Version Control | GitHub |

---

## Skills Demonstrated

- **Infrastructure as Code:** Provisioned AWS infrastructure (EKS, EC2, IAM, S3) using Terraform
- **Managed Kubernetes Provisioning:** Created and configured an Amazon EKS cluster for downstream workloads
- **Self-Hosted CI Runners:** Built and operated a self-hosted GitHub Actions runner on EC2
- **Privileged CI Execution:** Enabled secure execution of Terraform and kubectl from CI pipelines
- **Platform Tooling:** Installed and maintained Docker, kubectl, Terraform, and security tooling on the runner
- **Runtime Security Foundations:** Deployed Falco to EKS with alert forwarding via Falcosidekick UI

---

## Why a Self-Hosted Runner?

- Supports **custom tooling** (Falco, Trivy FS, SonarQube scanner).
- Enables full customisation of CI/CD behaviour.
- Suitable for projects that require **privileged operations**, such as:
  - Terraform apply  
  - Building & pushing Docker images  
  - Installing kernel-level tools (Falco)

