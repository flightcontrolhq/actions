# fcenv Setup Action

Installs the fcenv binary for loading environment variables from AWS Parameter Store and Secrets Manager.

## What is fcenv?

fcenv is a Go-based command wrapper that:
1. Fetches environment variables from AWS Parameter Store and Secrets Manager
2. Sets them in the process environment
3. Executes your command with those variables loaded

It supports concurrent fetching (up to 200 parallel requests) making it very fast for builds with many secrets.

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `fcenv-version` | fcenv Docker image version/tag to use | No | `latest` |

## Usage

```json
{
  "type": "uses",
  "uses": "flightcontrolhq/actions/fcenv",
  "with": {
    "fcenv-version": "latest"
  }
}
```

## How to Use fcenv After Installation

### 1. Set Environment Variables

```bash
export AWS_REGION=us-east-1
export FC_ENV_VARS_INLINE_JSON='{"VAR1":"value1","VAR2":{"fromParameterStore":"/path/to/param"}}'
```

### 2. Execute Commands with fcenv

```bash
# fcenv loads the env vars then executes your command
fcenv npm run build
fcenv docker build -t myapp:latest .
fcenv python script.py
```

## Configuration Format

fcenv accepts configuration via `FC_ENV_VARS_INLINE_JSON` environment variable:

```json
{
  "PLAIN_VAR": "literal-value",
  "PARAM_STORE_VAR": {
    "fromParameterStore": "/path/to/parameter"
  },
  "SECRET_VAR": {
    "fromSecretsManager": "secret-name"
  },
  "SECRET_JSON_VAR": {
    "fromSecretsManager": "arn:aws:secretsmanager:region:account:secret:name:json-key"
  }
}
```

### Variable Types

1. **Plain Text**: Direct string values
2. **Parameter Store**: References to SSM Parameter Store (auto-decrypted)
3. **Secrets Manager**: References to AWS Secrets Manager (supports JSON extraction)

## Example: Complete Build with fcenv

```bash
# Install fcenv
{
  "type": "uses",
  "uses": "flightcontrolhq/actions/fcenv"
}

# Configure and use fcenv
{
  "type": "command",
  "command": """
    export AWS_REGION=us-east-1
    export FC_ENV_VARS_INLINE_JSON='{
      "NODE_ENV": "production",
      "API_URL": {"fromParameterStore": "/myapp/api-url"},
      "DB_PASSWORD": {"fromSecretsManager": "prod/db-password"}
    }'

    # Build with secrets loaded
    fcenv npm ci
    fcenv npm run build
    fcenv npm test
  """
}
```

## Performance

- **Concurrent Fetching**: Up to 200 parallel AWS requests
- **Batch Operations**: Parameter Store fetched in batches of 10
- **Static Binary**: ~20-30MB Go binary with no dependencies
- **Multi-Architecture**: Supports linux/amd64 and linux/arm64

## IAM Permissions Required

### For Parameter Store
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:GetParameter",
        "ssm:GetParameters"
      ],
      "Resource": "arn:aws:ssm:*:*:parameter/*"
    },
    {
      "Effect": "Allow",
      "Action": "kms:Decrypt",
      "Resource": "arn:aws:kms:*:*:key/*"
    }
  ]
}
```

### For Secrets Manager
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": "arn:aws:secretsmanager:*:*:secret:*"
    }
  ]
}
```

## Advantages Over Other Solutions

- **Performance**: 200 concurrent AWS fetches vs sequential
- **Size**: Small static binary vs Node.js dependencies
- **Simplicity**: Just wraps commands, no complex configuration
- **Security**: Secrets never written to disk, only in memory
- **Compatibility**: Works with any command or build tool