# Soft-YT Organization Configuration

This repository contains centralized GitHub Actions workflows and organization-wide configuration for the `soft-yt` organization.

## Reusable Workflows

### Go Service Build and Deploy

Centralized CI/CD workflow for Go microservices that:
- Runs tests and linting
- Builds and pushes Docker images to GHCR
- Notifies about deployment status

**Location**: `.github/workflows/go-service-build.yaml`

**Usage in your service repository**:

```yaml
# .github/workflows/ci-cd.yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci-cd:
    uses: soft-yt/.github/.github/workflows/go-service-build.yaml@main
    with:
      service-name: your-service-name  # Used for ArgoCD link
      go-version: '1.21'                # Optional, defaults to 1.21
```

**Features**:
- ✅ Automated testing with `go test` and `go vet`
- ✅ Docker image build and push to GHCR
- ✅ Automatic image tagging (branch, SHA, latest)
- ✅ ArgoCD deployment notification
- ✅ No secrets required in service repos (uses `GITHUB_TOKEN`)

## Benefits

1. **Centralized Management**: Update CI/CD logic in one place, all services get the update
2. **Consistency**: All services use the same build process
3. **Security**: No need to copy secrets or tokens to each repository
4. **Simplicity**: Service repos only need a minimal workflow file

## Updating the Workflow

To update the workflow for all services:

1. Make changes to `.github/workflows/go-service-build.yaml`
2. Commit and push to `main` branch
3. All services using `@main` will automatically use the new version

For staged rollout, use git tags:
```yaml
uses: soft-yt/.github/.github/workflows/go-service-build.yaml@v1.0.0
```

## Integration with Backstage

All services created via Backstage automatically use this reusable workflow.

## Monitoring

- **GitHub Actions**: https://github.com/soft-yt/.github/actions
- **ArgoCD**: https://argo.dev.tulupov.org/applications
- **Grafana**: https://grafana.dev.tulupov.org/

---

🤖 Managed by Platform Team
