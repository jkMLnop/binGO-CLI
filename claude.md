# binGO-CLI — AI Context

CLI bingo client for connecting to a remote binGO server. Can also play
standalone (single-player, no networking).

Module: `github.com/jkMLnop/binGO-CLI` · Go 1.25.3 · No external dependencies
for standalone mode.

## Quick Commands

```bash
# Run all unit tests
go test ./...

# Build binary (version defaults to "dev")
go build -o binGO-CLI .

# Build binary with version injection
go build -ldflags "-X main.version=v9.4.0" -o binGO-CLI .

# Print version
./binGO-CLI -version

# Run modes
./binGO-CLI -mode standalone                         # local single-player
./binGO-CLI -mode client -server localhost:8080      # connect to server
./binGO-CLI -mode client -server bingo-server.fly.dev -code BINGO-XXXXX
```

## Architecture

### Package Layout

| Package | Purpose |
|---|---|
| `main` (bin.go) | CLI entry point: flag parsing, mode dispatch |
| `client/` | WebSocket client, auth manager, display, player actions |
| `shared/` | Board model, win detection, display formatting, buzzword loading |
| `standalone/` | Single-player offline mode (no networking) |

### Key Types

- **`shared.Board`** — bingo board with `MarkCell()`, `CheckWin()`, cell ID system

### WebSocket Protocol

Client sends `ClientMessage` (`json:"action"` = `"login"`, `"win"`, `"restart"`; fields: `username`, `token`, `code`).
Server sends `ServerMessage` (`json:"type"` = `"welcome"`, `"game_ended"`, `"player_joined"`, `"server_shutdown"`, `"error"`; fields include `buzzwords`, `players`, `token`, `code`, `winner`, `message`).

Players connect to `/ws`, send a login message with username + game code, receive a welcome with JWT token + buzzword grid. Board is generated client-side from the buzzword list.

## Conventions & Patterns

- **Error wrapping**: Use `fmt.Errorf("context: %w", err)` at all boundaries
- **Table-driven tests**: Use `t.Run()` for multiple cases
- **Pass `context.Context`**: As the first argument where applicable
- **Prefer explicit returns**: Over named return values

## Server Repository

All server, database, infrastructure, and web client code has moved to a
separate repository: **[github.com/jkMLnop/binGO](https://github.com/jkMLnop/binGO)**

That repo contains:
- WebSocket game server (`server/`)
- SQLite persistence (`db/`)
- Dockerfile, docker-compose, Fly.io configs
- Dagger CI/CD pipeline
- Web client (React SPA)
- Prometheus metrics, Grafana dashboards, OpenTelemetry tracing

## Files Worth Knowing

| File | Why |
|---|---|
| `bin.go` | Entry point, flag parsing, version var |
| `client/player.go` | WebSocket client connection and message handling |
| `client/auth.go` | Auth token management and login flow |
| `client/display.go` | Board rendering and welcome display |
| `client/menu.go` | Main menu for host/join/random |
| `shared/board.go` | Board model, cell marking, win detection |
| `shared/display.go` | Banner and board formatting |
| `standalone/player.go` | Single-player offline game mode |
| `CHANGELOG.md` | Detailed release history |
| `docs/ROADMAP.md` | TODO items by phase |
