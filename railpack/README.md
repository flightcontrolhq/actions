# Railpack Setup Action

Installs the Railpack CLI and configures the shared dependencies used by runner image builds.

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `railpack-version` | Railpack version to install. This maps to the `railpack_version` build input. | No | `0.29.0` |

## Usage

```json
{
  "type": "uses",
  "uses": "flightcontrolhq/actions/railpack",
  "with": {
    "railpack-version": "0.29.0"
  }
}
```

## What it does

1. **Installs Railpack**: Downloads and installs the specified Railpack release binary
2. **Sets up shared build dependencies**: Ensures Docker, curl, and tar are available
3. **Verifies installation**: Confirms Railpack is properly installed with `railpack --version`

## Build Command Usage

After setting up with this action, build instructions can prepare a Railpack build plan:

```bash
railpack prepare ./path/to/project --plan-out railpack-plan.json --info-out railpack-info.json
```

Production image builds should use the generated plan with Docker BuildKit and the matching Railpack frontend version.

## Prerequisites

- Docker must be installed and running
