# DevOps — binGO-CLI

This repository contains only the CLI client code. All server, database,
infrastructure, and CI/CD pipeline code has moved to
**[github.com/jkMLnop/binGO](https://github.com/jkMLnop/binGO)**.

## What's Here

- **`go test ./...`** — single command, runs all unit tests (no tags needed)
- **`go build -o binGO-CLI .`** — builds the binary

## CI

A simple GitHub Actions workflow (`.github/workflows/ci.yml`) runs `go test ./...`
and `go build` on every push/PR to main. No secrets, no deployment, no containers.

## Releases

Prebuilt binaries are attached to GitHub Releases. Build manually:

```bash
go build -ldflags "-X main.version=vX.Y.Z" -o binGO-CLI .
```

## Required Secrets

| Secret | Where | Purpose |
|---|---|---|
| `FLY_API_TOKEN` | GitHub repo secrets + local env | Fly.io deployment |
| `GHCR_TOKEN` | Local env (CI uses `GITHUB_TOKEN`) | Push Docker images to ghcr.io |
| `GH_TOKEN` | Local env (CI uses `GITHUB_TOKEN`) | Create GitHub Releases |
| `ADMIN_API_KEY` | Fly.io secrets (both apps) | Admin API authentication in deployed server |

## Version Injection

Every deployed binary knows its exact version:

```
./binGO -version    # prints "v8.2.0" or "abc1234" (short SHA)
```

Injected at build time via `-ldflags "-X main.version=<value>"`:
- `bin.go` declares `var version = "dev"` (default for local builds)
- `Dockerfile` accepts `ARG VERSION=dev` and passes it to `go build`
- Dagger's `build` function passes the version arg through
- CI sets version to the git short SHA (staging) or tag name (production)

## Fly.io Setup (One-Time)

```bash
# Create staging app
flyctl apps create bingo-server-staging --org personal
flyctl volumes create bingo_data --region sjc --app bingo-server-staging
flyctl secrets set ADMIN_API_KEY=<your-key> --app bingo-server-staging

# Production already exists; ensure secrets are set
flyctl secrets set ADMIN_API_KEY=<your-key> --app bingo-server
```
