# Nixpacks Setup Action

Installs Nixpacks binary and configures the environment for building applications with automatic language detection.

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `nixpacks-version` | Nixpacks version to install | No | `1.21.3` |
| `ecr-registry` | ECR registry URL for pushing images | Yes | - |
| `aws-region` | AWS region for ECR | No | `us-east-1` |

## Environment Variables

AWS credentials should be configured via IAM role or environment variables:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_SESSION_TOKEN` (for temporary credentials)

## Usage

```json
{
  "type": "uses",
  "uses": "flightcontrolhq/actions/nixpacks",
  "with": {
    "nixpacks-version": "1.21.3",
    "ecr-registry": "123456789.dkr.ecr.us-east-1.amazonaws.com",
    "aws-region": "us-east-1"
  }
}
```

## What it does

1. **Installs Nixpacks**: Downloads and installs the specified version of Nixpacks
2. **ECR authentication**: Logs into Amazon ECR for pushing built images
3. **Verifies installation**: Confirms Nixpacks is properly installed
4. **Sets up Docker buildx**: Configures buildx for multi-platform support (Nixpacks uses Docker internally)

## Nixpacks Features

Nixpacks automatically detects and builds applications in these languages:
- Node.js/JavaScript/TypeScript
- Python
- Go
- Ruby
- Java
- PHP
- Rust
- .NET
- And more...

## Build Command Usage

After setting up with this action, you can build with Nixpacks:

```bash
# Basic build
nixpacks build . --name myapp:latest

# With custom build command
nixpacks build . --name myapp:latest --build-cmd "npm run build"

# With custom start command
nixpacks build . --name myapp:latest --start-cmd "npm start"

# With environment file
nixpacks build . --name myapp:latest --env-file .env.production
```

## Prerequisites

- Docker must be installed and running
- AWS CLI must be available
- Valid AWS credentials with ECR permissions
- Sufficient disk space for Docker images