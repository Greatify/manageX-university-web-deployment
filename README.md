# ManageX-University Web Deployment

This repository contains the deployment configurations and CI/CD workflows for the ManageX-University web application .

## Repository Structure

```
manageX-university-web-deployment/
├── .github/
│   └── workflows/
│       ├── cd-dev.yaml        # Development environment CI/CD
│       ├── ci-cd-stage.yaml   # Staging environment CI/CD
│       └── ci-cd-prod.yaml    # Production environment CI/CD
├── k8s/
│   ├── base/                  # Base Kubernetes configurations
│   │   ├── kustomization.yaml
│   │   ├── deployment/        # Deployment configurations
│   │   ├── service/           # Service configurations
│   │   ├── ingress/           # Ingress configurations
│   │   ├── configmap/         # ConfigMap configurations
│   │   ├── cronjob/           # CronJob configurations
│   │   ├── hpa/               # Auto-scaling configurations
│   │   ├── secretproviderclass/ # Secret management
│   └── overlays/              # Environment-specific overlays
│       ├── dev/               # Development environment
│       ├── stage/             # Staging environment
│       ├── prod/              # Production environment
│       └── gulbarga/          # Additional environment
├── .gitignore
└── .DS_Store
```

## CI/CD Pipeline

The deployment process is automated using GitHub Actions with separate workflows for different environments:

### Deployment Trigger Methods
- Workflows are triggered manually via GitHub `workflow_dispatch` with password protection for staging and production.
- Manual triggering for staging and production requires entering a deployment password.
- Failed authentication attempts are logged and can be reported (e.g., via Slack).
- This adds an additional layer of security for deployments.

### Development Environment
- Triggered manually with Docker image and SHA inputs.
- Deploys the specified Docker image to the development Kubernetes cluster.
- Updates the `kustomization.yaml` file with the new image tag.
- Success/failure notifications can be sent via Slack or other channels.

### Staging Environment
- Triggered manually with password protection.
- Builds and pushes Docker images to a container registry (e.g., Amazon ECR).
- Updates the `kustomization.yaml` file with the new image tag.
- Deploys to the staging Kubernetes cluster.
- Security measures and notifications are in place.

### Production Environment
- Triggered manually with password protection.
- Similar to staging process with production-specific configurations.
- Additional security measures for production deployments.

## Kubernetes Deployment

The application is deployed using Kubernetes with Kustomize for environment-specific configurations:

- **Deployment**: Manages the application pods.
- **Service**: Exposes the application within the cluster.
- **Ingress**: Handles external access to the application.
- **ConfigMap**: Manages environment-specific configurations.
- **Auto-scaling (HPA)**: Ensures application scalability based on demand.
- **Secret Management**: Handles sensitive data using SecretProviderClass.

## Prerequisites

- Access to a container registry (e.g., AWS ECR, Docker Hub)
- Kubernetes cluster (e.g., EKS, GKE, AKS)
- GitHub repository access
- Required secrets configured in GitHub:
  - REGISTRY_ACCESS_KEY / AWS_ACCESS_KEY_ID
  - REGISTRY_SECRET_KEY / AWS_SECRET_ACCESS_KEY
  - REGISTRY_REGION / AWS_REGION
  - REPO_DISPATCH_TOKEN
  - SLACK_WEBHOOK_URL (if using Slack notifications)
  - DEPLOY_PASSWORD

## Deployment Process

1. For development deployments, specify Docker image and SHA in workflow inputs.
2. For staging/production deployments, authenticate with deployment password.
3. GitHub Actions workflow is triggered.
4. For staging/production, Docker image is built and pushed to the registry.
5. Kubernetes configurations are updated with new image tags.
6. Application is deployed to the target environment.
7. Deployment status is notified via Slack or other channels.

## Monitoring and Alerts

- Deployment status notifications can be sent to Slack.
- Failed deployments trigger alerts with detailed information.
- Success notifications include deployment details and tag the responsible user.
- Security alerts for unauthorized deployment attempts.
- Docker build failures are reported separately.

## Security Considerations

- Manual deployments for staging/production are password-protected.
- Secrets are managed through a secure secret manager (e.g., AWS Secrets Manager).
- Secure token management for repository access.
- Environment-specific configurations are isolated.
- Unauthorized deployment attempts are logged and reported.
- Sensitive files are cleaned up after builds.

## Contributing

1. Create a feature branch.
2. Make your changes.
3. Submit a pull request.

## Support

For any issues or questions regarding the deployment process, please contact the DevOps team.
