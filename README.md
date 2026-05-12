![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonwebservices&logoColor=white)
![Amazon ECR](https://img.shields.io/badge/Amazon_ECR-FF9900?style=for-the-badge&logo=amazonecr&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
# aws-gitops-platform-app

Application source for a GitOps-driven Kubernetes platform on AWS. This repo holds the API and worker service code and the CI pipelines that build them. All Kubernetes configuration and infrastructure lives in the companion repo: [aws-gitops-platform-config](https://github.com/selimcelem/aws-gitops-platform-config).

## Architecture

Two services run on EKS behind a GitOps deployment flow:

- **API service**: HTTP endpoint that accepts job submissions and publishes them to an SQS queue.
- **Worker service**: Consumes from SQS and writes processed records to RDS PostgreSQL.

On every push to main:

1. GitHub Actions builds Docker images for changed services.
2. Images are pushed to ECR using OIDC authentication, with no long-lived AWS credentials.
3. The pipeline updates image tags in the config repo.
4. ArgoCD detects the change and syncs the cluster.

## API contract

POST /jobs accepts {"payload": "..."} and returns {"job_id": "...", "status": "queued"}.
GET /healthz returns 200 for liveness checks.

## Worker behavior

Reads SQS messages, writes (job_id, payload, processed_at, status) to the jobs table in PostgreSQL, deletes the message on success.

## Repository layout

```
aws-gitops-platform-app/
├── .github/workflows/   # CI for image build, push, and config repo image-tag bump
├── services/
│   ├── api/             # FastAPI service: POST /jobs, GET /healthz
│   └── worker/          # SQS consumer that writes to RDS
├── BUILD_LOG.md
├── PORTFOLIO.md
└── README.md
```

## Status

Project scaffolded. Service code and CI workflows in progress.

## Project brief

The full requirements for this project are in [PROJECT_BRIEF.md](PROJECT_BRIEF.md).

## Architecture decisions

Major architecture decisions, including the choice to self-install ArgoCD via Terraform `helm_release` (rather than the AWS-managed EKS Capability) and the two-pipeline CI/CD pattern, are documented in the config repo at [docs/adr/](https://github.com/selimcelem/aws-gitops-platform-config/tree/main/docs/adr).
