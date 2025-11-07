# Docker Setup Action

Sets up Docker with buildx for multi-platform builds and authenticates with Amazon ECR and optionally Docker Hub.

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `ecr-registry` | ECR registry URL (e.g., `123456789.dkr.ecr.us-east-1.amazonaws.com`) | Yes | - |
| `aws-region` | AWS region for ECR | No | `us-east-1` |
| `docker-hub-login` | Enable Docker Hub login (requires env vars) | No | `false` |

## Environment Variables

For Docker Hub login:
- `DOCKER_USERNAME`: Docker Hub username
- `DOCKER_PASSWORD`: Docker Hub password or access token

AWS credentials should be configured via IAM role or environment variables:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN` (for temporary credentials)

## Usage

```json
{
  "type": "uses",
  "uses": "flightcontrolhq/actions/docker",
  "with": {
    "ecr-registry": "123456789.dkr.ecr.us-east-1.amazonaws.com",
    "aws-region": "us-east-1",
    "docker-hub-login": "true"
  }
}
```

## What it does

1. **Installs Docker buildx**: Ensures buildx is available for multi-platform builds
2. **Creates buildx builder**: Sets up a container-based builder named `fc-builder`
3. **Configures platforms**: Supports `linux/amd64` and `linux/arm64`
4. **ECR authentication**: Logs into Amazon ECR using AWS credentials
5. **Docker Hub authentication**: Optionally logs into Docker Hub to avoid rate limits

## Prerequisites

- Docker must be installed on the system
- AWS CLI must be available
- Valid AWS credentials with ECR permissions
- For Docker Hub: Valid Docker Hub credentials in environment variables