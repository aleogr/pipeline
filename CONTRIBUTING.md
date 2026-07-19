# Contributing

Maintainer notes for this repository. Usage of the reusable workflows is documented in the [README](README.md).

## Conventions

- Conditionals are declarative gates at the job boundary (`if: ${{ inputs.enable-* }}`) — no branching inside steps.
- Each reusable-workflow job runs exactly one composite action (`go-lint`, `go-test`, ...). The actions embed their own checkout, so never compose two in the same job.
- Input names follow `<category>-<subject>`: `enable-lint`, `version-go`, `timeout-test`.
- Third-party actions are pinned by commit SHA. The `# vN` comment next to the SHA is kept up to date by Dependabot — do not remove it.

## Implementation notes

- `GOPRIVATE` is exported via `GITHUB_ENV` by `go-setup`, so it applies to all later steps of the job, not only the steps inside the action.
- GoReleaser follows `~> v2`; golangci-lint is pinned exactly. Both can be overridden via action inputs.
- The CI Build job is the `go-release` action in `--snapshot --skip=docker --skip=sign` mode — a compile smoke test, not a release.
- Every Go action/workflow accepts `workdir` (default `.`) for monorepo support; the sample projects live in [`examples/go-flat/`](examples/go-flat/) and [`examples/go-structured/`](examples/go-structured/).

## Versioning

- Consumers pin by tag (`@v1`). Move the `v1` tag on every compatible release; breaking changes become `v2`.
- Internal references (workflow → action, action → action) use `@main`, so even a tagged workflow runs the actions from `main`. Because of this:
  - treat `main` as a release channel — only merge what could go to production;
  - a change that adds an action input and a change that uses it must land in two separate PRs, since a PR's jobs run the actions from `main`.
- This repository's own workflows use local references (`uses: ./.github/workflows/ci-go.yml`) so a PR validates its own version of the workflows.

## CI of this repository

- `ci.yml` runs the full Go pipeline against both example projects, validating the actions/workflows against the flat and the structured layout.
- `lint-workflows.yml` runs [actionlint](https://github.com/rhysd/actionlint) on PRs touching `.github/`.

## Adding a new technology

1. Composite actions under `.github/actions/<tech>-*` (reuse `git-setup`/`docker-setup`/`cosign-setup`).
2. `ci-<tech>.yml` and `cd-<tech>.yml` with `workflow_call`, following the `enable-*` pattern.
3. Document the interface in the README and publish a tag.

## Roadmap

- Benchmark comparison against a baseline (benchstat) instead of a standalone run.
