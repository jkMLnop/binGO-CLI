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

Version injection at build time (default `"dev"`):

```bash
./binGO-CLI -version    # prints "vX.Y.Z" or "dev"
```
