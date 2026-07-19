# pipeline

<img width="1024" height="559" alt="playground" src=".github/assets/banner.png" />

</details>

## Continuous Integration

<!-- Golang -->

### Golang

Lint, test, benchmark and build checks on every pull request — feedback before merge, nothing published.

```yaml
name: Continuous Integration

on:
  pull_request:
    branches: [ main ]

permissions:
  contents: read

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  CI-Go:
    uses: aleogr/pipeline/.github/workflows/ci-go.yml@v1
    secrets:
      GO_MODULES_TOKEN: ${{ secrets.GO_MODULES_TOKEN }}
```

<details name="optionals">

<summary>Optional secrets</summary>

<br />

> **`GO_MODULES_TOKEN`** · If the project imports private Go modules from **other** repositories in the organization, provide a PAT.

</details>

<details name="optionals">

<summary>Optional inputs</summary>

<br />

> **`enable-lint`** · `true` · Lint job (golangci-lint)

> **`enable-test`** · `true` · Test job with coverage (HTML artifact)

> **`enable-bench`** · `false` · Benchmark job

> **`enable-build`** · `true` · Compile targets without publishing anything and without Docker image builds

> **`timeout-lint`** · `10` · Lint job timeout, in minutes

> **`timeout-test`** · `15` · Test job timeout, in minutes

> **`timeout-bench`** · `20` · Bench job timeout, in minutes

> **`timeout-build`** · `15` · Build job timeout, in minutes

> **`args-test`** · `-race -covermode=atomic` · Extra go test args

> **`args-build`** · `--clean --snapshot --skip=docker --skip=sign` · GoReleaser args for the Build job

> **`retention-coverage`** · `7` · Coverage artifact retention, in days

> **`version-go`** · `stable` · Go version

> **`goprivate`** · `github.com/aleogr/*` · GOPRIVATE pattern for private modules

> **`workdir`** · `.` · Working directory of the Go module (monorepo support)

</details>

<br />

## Continuous Delivery

<!-- Golang -->

### Golang

Tag-triggered release: GoReleaser publishes binaries and pushes the Docker image to the registry.

```yaml
name: Continuous Delivery

on:
  push:
    tags: [ 'v*' ]

permissions:
  contents: write
  packages: write
  id-token: write

concurrency:
  group: ${{ github.workflow }}

jobs:
  CD-Go:
    uses: aleogr/pipeline/.github/workflows/cd-go.yml@v1
    secrets:
      GO_MODULES_TOKEN: ${{ secrets.GO_MODULES_TOKEN }}
      REGISTRY_PASSWORD: ${{ secrets.REGISTRY_PASSWORD }}
```

<details name="optionals">

<summary>Optional secrets</summary>

<br />

> **`GO_MODULES_TOKEN`** · If the project imports private Go modules from **other** repositories in the organization, provide a PAT.

> **`REGISTRY_PASSWORD`** · Credential for registries other than ghcr.io (Docker Hub, for example).

</details>

<details name="optionals">

<summary>Optional inputs</summary>

<br />

> **`enable-release`** · `true` · Release job (GoReleaser)

> **`enable-docker`** · `true` · Registry login + image build/push. When false, injects --skip=docker into GoReleaser

> **`timeout-release`** · `30` · Release job timeout, in minutes

> **`args-release`** · `--clean` · GoReleaser args

> **`registry`** · `ghcr.io` · Container Registry for login/push

> **`registry-username`** · `github.actor` · Registry username

> **`version-go`** · `stable` · Go version

> **`goprivate`** · `github.com/aleogr/*` · GOPRIVATE pattern for private modules

> **`workdir`** · `.` · Working directory of the Go module (monorepo support)

</details>

<br />

---

> [!IMPORTANT]
> CD pipeline - The Release job expects a `.goreleaser.yaml` and a `Dockerfile` - use the ones in [`examples/go-flat/`](examples/go-flat/) (everything at the module root) or [`examples/go-structured/`](examples/go-structured/) (entrypoint under `cmd/`, Dockerfile under `build/`). For modules outside the repository root, see the `workdir` input.

> [!TIP]
> Copy your chosen `.yml` template into `.github/workflows/`.
