# Reusable Workflows for YT-Soft Organization

This repository contains centralized GitHub Actions reusable workflows for the `soft-yt` organization. Use these workflows to maintain consistency, reduce duplication, and centralize CI/CD management across all projects.

## 🚀 Available Workflows

### 1. Go CI (`go-ci.yaml`)

Comprehensive CI for Go projects with testing, linting, and coverage checks.

**Features**:
- ✅ Unit tests with race detection
- ✅ Integration tests (optional)
- ✅ Coverage threshold enforcement
- ✅ golangci-lint
- ✅ Binary build
- ✅ Codecov integration

**Usage**:
```yaml
jobs:
  backend:
    uses: soft-yt/reusable-workflows/.github/workflows/go-ci.yaml@main
    with:
      go-version: '1.24'                 # Optional, default: 1.24
      working-directory: '.'             # Optional, default: .
      run-integration-tests: true        # Optional, default: false
      coverage-threshold: 70             # Optional, default: 70
```

**Inputs**:
- `go-version`: Go version to use (default: `1.24`)
- `working-directory`: Working directory for Go project (default: `.`)
- `run-integration-tests`: Run integration tests (default: `false`)
- `coverage-threshold`: Minimum coverage % (default: `70`)

---

### 2. Frontend CI (`frontend-ci.yaml`)

CI for React/TypeScript frontend projects.

**Features**:
- ✅ ESLint
- ✅ TypeScript type checking
- ✅ Unit tests with coverage
- ✅ Production build
- ✅ Multiple package managers (npm, yarn, pnpm)

**Usage**:
```yaml
jobs:
  frontend:
    uses: soft-yt/reusable-workflows/.github/workflows/frontend-ci.yaml@main
    with:
      node-version: '20'                 # Optional, default: 20
      working-directory: 'frontend'      # Optional, default: frontend
      package-manager: 'npm'             # Optional, default: npm
      run-type-check: true               # Optional, default: true
```

**Inputs**:
- `node-version`: Node.js version (default: `20`)
- `working-directory`: Frontend directory (default: `frontend`)
- `package-manager`: npm, yarn, or pnpm (default: `npm`)
- `run-type-check`: Run TypeScript checks (default: `true`)

---

### 3. Security Scan (`security-scan.yaml`)

Comprehensive security scanning for Go projects and Docker images.

**Features**:
- ✅ gosec (Go security scanner)
- ✅ Trivy filesystem scan
- ✅ Trivy Docker image scan (optional)
- ✅ Dependency review
- ✅ SARIF upload to GitHub Security

**Usage**:
```yaml
jobs:
  security:
    uses: soft-yt/reusable-workflows/.github/workflows/security-scan.yaml@main
    with:
      go-version: '1.24'                 # Optional, default: 1.24
      scan-go: true                      # Optional, default: true
      scan-docker: false                 # Optional, default: false
      docker-image: ''                   # Required if scan-docker: true
```

**Inputs**:
- `go-version`: Go version (default: `1.24`)
- `working-directory`: Working directory (default: `.`)
- `scan-go`: Run Go security scans (default: `true`)
- `scan-docker`: Scan Docker images (default: `false`)
- `docker-image`: Docker image to scan (required if scan-docker: true)

---

### 4. Docker Build (`docker-build.yaml`)

Universal Docker build and push to container registry.

**Features**:
- ✅ Multi-platform builds (amd64, arm64)
- ✅ Docker Buildx
- ✅ Build cache optimization
- ✅ Automatic tagging (branch, SHA, semver, latest)
- ✅ SBOM and attestation
- ✅ GitHub Container Registry (GHCR)

**Usage**:
```yaml
jobs:
  build:
    uses: soft-yt/reusable-workflows/.github/workflows/docker-build.yaml@main
    with:
      registry: ghcr.io                  # Optional, default: ghcr.io
      platforms: linux/amd64,linux/arm64 # Optional
      push: true                         # Optional, default: true
```

**Inputs**:
- `registry`: Container registry (default: `ghcr.io`)
- `image-name`: Image name (default: repository name)
- `dockerfile`: Path to Dockerfile (default: `Dockerfile`)
- `context`: Build context (default: `.`)
- `platforms`: Target platforms (default: `linux/amd64,linux/arm64`)
- `push`: Push to registry (default: `true`)
- `build-args`: Build arguments

**Outputs**:
- `image-tags`: Built image tags
- `image-digest`: Image digest

---

### 5. Quality Gate (`quality-gate.yaml`)

Aggregates CI results and enforces quality standards.

**Features**:
- ✅ Checks all CI job results
- ✅ PR comments with status
- ✅ GitHub Step Summary
- ✅ Fail on critical issues
- ✅ Warn on non-critical issues

**Usage**:
```yaml
jobs:
  quality-gate:
    needs: [backend, frontend, security]
    uses: soft-yt/reusable-workflows/.github/workflows/quality-gate.yaml@main
    with:
      backend-result: ${{ needs.backend.result }}
      frontend-result: ${{ needs.frontend.result }}
      security-result: ${{ needs.security.result }}
      comment-pr: true                   # Optional, default: true
```

**Inputs**:
- `backend-result`: Backend CI result (required)
- `frontend-result`: Frontend CI result (default: `skipped`)
- `security-result`: Security scan result (required)
- `comment-pr`: Comment on PR (default: `true`)

---

## 📖 Complete Example

Here's a complete CI/CD workflow using all reusable workflows:

```yaml
# .github/workflows/ci.yaml
name: CI

on:
  push:
    branches: [ main, master, develop ]
  pull_request:
    branches: [ main, master, develop ]

permissions:
  contents: read
  pull-requests: write
  security-events: write

jobs:
  backend:
    uses: soft-yt/reusable-workflows/.github/workflows/go-ci.yaml@main
    with:
      go-version: '1.24'
      run-integration-tests: true
      coverage-threshold: 70

  frontend:
    uses: soft-yt/reusable-workflows/.github/workflows/frontend-ci.yaml@main
    with:
      node-version: '20'
      working-directory: 'frontend'

  security:
    uses: soft-yt/reusable-workflows/.github/workflows/security-scan.yaml@main
    with:
      scan-go: true

  quality-gate:
    needs: [backend, frontend, security]
    if: always()
    uses: soft-yt/reusable-workflows/.github/workflows/quality-gate.yaml@main
    with:
      backend-result: ${{ needs.backend.result }}
      frontend-result: ${{ needs.frontend.result }}
      security-result: ${{ needs.security.result }}
```

```yaml
# .github/workflows/build-deploy.yaml
name: Build and Deploy

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

permissions:
  contents: write
  packages: write

jobs:
  build:
    uses: soft-yt/reusable-workflows/.github/workflows/docker-build.yaml@main
    with:
      platforms: linux/amd64,linux/arm64
```

---

## 🎯 Benefits

### 1. Centralized Management
Update CI/CD logic in one place, all services get the update automatically.

### 2. Consistency
All services use the same tested workflows and best practices.

### 3. Security
- No secrets duplication across repositories
- Uses `GITHUB_TOKEN` automatically
- Centralized security scanning

### 4. Simplicity
Service repositories only need minimal workflow files (3-10 lines).

### 5. DRY Principle
Don't repeat yourself - write once, use everywhere.

---

## 🔄 Updating Workflows

### For All Services (Immediate)
```bash
# Make changes to workflows
git commit -m "feat: improve security scanning"
git push origin main
```
All services using `@main` will use the new version immediately.

### For Staged Rollout
```bash
# Tag a version
git tag v1.2.0
git push origin v1.2.0
```

Services can pin to specific versions:
```yaml
uses: soft-yt/reusable-workflows/.github/workflows/go-ci.yaml@v1.2.0
```

---

## 🏢 Integration with Backstage

All services created via Backstage Software Templates automatically use these reusable workflows. No manual configuration needed!

**Template Examples**:
- `backend-service-go`
- `frontend-service-react`
- `bff-service-go` ⭐ (recommended)

---

## 📊 Monitoring

- **GitHub Actions**: https://github.com/soft-yt/reusable-workflows/actions
- **ArgoCD**: https://argo.dev.tulupov.org/applications
- **Grafana**: https://grafana.dev.tulupov.org/

---

## 🤝 Contributing

To add a new reusable workflow:

1. Create workflow in `.github/workflows/`
2. Use `workflow_call` trigger
3. Document all inputs/outputs
4. Add usage example to this README
5. Test with a service repository
6. Create PR for review

---

🤖 Managed by Platform Team | YT-Soft Organization
