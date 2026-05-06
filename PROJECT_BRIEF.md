# Project Brief: GitOps Platform with Observability on AWS EKS

**Type:** Cloud Portfolio Project
**Source:** Industry-sourced mock project brief

## Overview

A production-grade Kubernetes platform on AWS demonstrating GitOps-based deployments, event-driven architecture, and full observability. The focus is platform engineering and operational maturity, not the application itself.

## Business Context

A development team needs a Kubernetes platform where application deployments are fully driven by Git, infrastructure changes are auditable and reproducible, and the health of all services is visible through a monitoring dashboard.

## Architecture

Two separate GitHub repositories:

**Repo 1: Application repo**

- Contains application source code and Dockerfiles.
- GitHub Actions pipeline builds images and pushes to ECR on every push to main.
- Pipeline updates the image tag in Repo 2 automatically.

**Repo 2: Config repo**

- Contains all Kubernetes manifests and Helm charts.
- ArgoCD watches this repo and syncs state to the cluster automatically.
- No manual kubectl apply ever needed.

## Services

Three services running in the cluster:

- **API service:** accepts HTTP requests and puts messages on an SQS queue.
- **Worker service:** reads from the SQS queue and writes results to the database.
- **Database:** RDS PostgreSQL for persistent storage.

## Requirements

### Infrastructure (Terraform modules)

- VPC with 3 public and 3 private subnets, NAT Gateway, routing tables.
- EKS cluster with managed node group in private subnets.
- ECR repositories for each service.
- RDS PostgreSQL in private subnets.
- SQS queue.
- IAM roles with IRSA for pod-level AWS access, no credentials stored anywhere.
- All modules with outputs.tf exposing key values.

### GitOps (ArgoCD)

- Installed on the cluster via Terraform or Helm.
- Configured to watch the config repo.
- Automatic sync on config repo changes.
- Kustomize for environment-specific configuration.

### CI/CD (GitHub Actions)

- OIDC authentication to AWS, no long-lived credentials.
- Build and push Docker images to ECR on push to main.
- Automatic image tag update in config repo to trigger ArgoCD sync.

### Observability

- Prometheus installed on the cluster, scraping all services.
- Grafana dashboards for cluster health, pod metrics, and SQS queue depth.
- Screenshots of dashboards committed to the repo for portfolio evidence.

### Helm

- Custom Helm charts for each service in the config repo.
- ArgoCD deploys via Helm.

### Cost management

- All infrastructure provisioned with terraform apply.
- Torn down with terraform destroy after each session.
- RDS on smallest available instance, single AZ.

## Stack

AWS, EKS, ECR, RDS, SQS, IAM, VPC, Terraform, ArgoCD, Kustomize, Helm, Prometheus, Grafana, GitHub Actions, Docker, Python.

## Repositories

- Application code: [aws-gitops-platform-app](https://github.com/selimcelem/aws-gitops-platform-app)
- Platform configuration: [aws-gitops-platform-config](https://github.com/selimcelem/aws-gitops-platform-config)
