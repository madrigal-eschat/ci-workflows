# ci-workflows

Shared reusable GitHub Actions workflows.

These previously lived in `dev-events-mqtt` alongside the protocol spec. They are
build plumbing with no connection to that spec, and sharing a repo meant sharing a
single release train — so the spec could not be versioned without also versioning
the workflows. They live here now, and version independently.

## Available workflows

| Workflow | Purpose | Key inputs |
|---|---|---|
| `_commitlint.yml` | Conventional Commits check over a commit range | `from-sha`, `to-sha` |
| `_prose-lint.yml` | `alex` prose lint | `files` (default `README.md`) |
| `_kotlin-ci.yml` | Kotlin build, lint and test | — |
| `_kotlin-coverage.yml` | Kotlin coverage report on PRs | `base-ref` |
| `_rust-ci.yml` | `fmt`, `check`, `clippy`, `test` | — |
| `_rust-coverage.yml` | Rust coverage report on PRs | `base-ref` |
| `_rust-release-assets.yml` | Build a binary and attach it to a release | `binary-name`, `release-tag` |
| `_semrel.yml` | semantic-release; outputs `released`, `version`, `tag` | `working-directory` |

## Usage

Pin every reference to an immutable commit SHA, with the version in a trailing
comment. Renovate keeps the SHA current:

```yaml
jobs:
  ci:
    uses: madrigal-eschat/ci-workflows/.github/workflows/_rust-ci.yml@<sha> # v1.0.0
```

Do not reference `@main`. A bad push here would otherwise reach every consumer
immediately, with no version to roll back to.

`_semrel.yml` takes an optional `GH_TOKEN` secret — a fine-grained PAT with
`contents`, `issues` and `pull-requests` write. Without it the workflow falls back
to `GITHUB_TOKEN`, and release commits will not trigger downstream workflows.

Cross-repository reusable workflows require this repository to stay public, or the
calling repository to be granted access explicitly.

## Templates

`examples/` holds ready-to-copy starting points:

- `rust-project-ci.yml`, `kotlin-project-ci.yml` — full `ci.yml` for a consumer repo
- `rust-releaserc.json`, `kotlin-releaserc.json` — matching semantic-release config

## Contributing

Commits follow Conventional Commits; `commitlint` enforces this on pull requests.
`actionlint` runs against every workflow. Releases are cut by semantic-release on
push to `main`.
