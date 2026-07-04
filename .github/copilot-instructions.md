# GitHub Copilot Instructions — binGO-CLI

## Project Overview

CLI bingo client. Module: `github.com/jkMLnop/binGO-CLI` · Go 1.25.3 · No external dependencies for standalone mode.

Full architecture, commands, and context: see `claude.md` at the repo root.

**Server code has moved to [github.com/jkMLnop/binGO](https://github.com/jkMLnop/binGO).** This repo is a pure CLI client with standalone and WebSocket client modes.

## Branching Strategy (GitHub Flow + Git Worktrees)

- `main` is always releasable. CI runs tests on every push/PR to main.
- New work goes on short-lived `feat/<name>` branches.
- Never commit directly to `main`. Always go via a `feat/` branch.

**Parallel development via git worktrees:** Each `feat/` branch lives in its own worktree directory.

```bash
git worktree add ../binGO-feat-<name> -b feat/<name>
# Open ../binGO-feat-<name> in a new VS Code window

# After merging to main, clean up
git worktree remove ../binGO-feat-<name>
git branch -d feat/<name>
```

## Testing

- Every new function or behaviour must have a corresponding unit test.
- Unit tests live alongside source files (`*_test.go`).
- Run with: `go test ./...`

## Code Quality

### No Dead Code
Remove unused functions, variables, imports, and types rather than commenting them out.

### No Duplicate Logic
Before adding a helper, check whether the logic already exists. Centralise shared logic in the `shared/` package. Avoid copy-pasting logic across packages.

### Idiomatic Go
- Wrap errors at boundaries: `fmt.Errorf("context: %w", err)`
- Use table-driven tests with `t.Run()` for multiple cases
- Pass `context.Context` as the first argument where applicable
- Prefer explicit returns over named return values
