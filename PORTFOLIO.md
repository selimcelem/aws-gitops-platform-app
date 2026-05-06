# GitOps Platform with Observability on AWS EKS

A production-grade Kubernetes platform on AWS, built to demonstrate the operational maturity expected of a platform or DevOps engineer: GitOps-driven deployments, event-driven architecture, full observability, and zero manual cluster operations.

## What this project demonstrates

- **GitOps as the single deployment path.** ArgoCD reconciles cluster state from a config repository on every change. No engineer ever runs kubectl apply against this cluster.
- **Two-repo separation of concerns.** Application source and Kubernetes configuration live in separate repositories with a clean handoff: CI builds an image, pushes to ECR, and bumps the tag in the config repo. ArgoCD takes it from there.
- **Event-driven architecture.** The API service publishes to SQS. A worker consumes and writes to RDS PostgreSQL. Decoupled, observable, scalable.
- **Infrastructure as modular Terraform.** Every component (VPC, EKS, ECR, RDS, SQS, IAM, ArgoCD) is its own module with explicit inputs and outputs. The whole platform comes up with a single terraform apply and tears down cleanly with terraform destroy.
- **Identity without long-lived credentials.** GitHub Actions authenticates to AWS via OIDC. Pods authenticate via IRSA. No static keys exist anywhere in the system.
- **Full observability.** Prometheus scrapes the cluster and services. Grafana dashboards cover cluster health, pod metrics, and SQS queue depth.

## Stack

AWS (EKS, ECR, RDS, SQS, IAM, VPC), Terraform, ArgoCD, Helm, Kustomize, Prometheus, Grafana, GitHub Actions, Docker, Python.

## Repositories

- Application code: this repository.
- Platform configuration: [aws-gitops-platform-config](https://github.com/selimcelem/aws-gitops-platform-config).

## My background

I am a career switcher from BIM engineering into cloud and platform engineering. Earlier portfolio work includes:

- **aws-eks-pipeline:** EKS with Terraform modules, Helm, GitHub Actions OIDC, ECR, VPC with public and private subnets and NAT.
- **mini-cloud-deployment-platform:** ECS Fargate, ECR, ALB, Terraform, GitHub Actions.

This project extends that work into a GitOps-first, observable, event-driven platform.

## Status

Scaffolding committed. Infrastructure and application code in progress.
