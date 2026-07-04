```bash
 /$$       /$$            /$$$$$$   /$$$$$$ 
| $$      |__/           /$$__  $$ /$$__  $$
| $$$$$$$  /$$ /$$$$$$$ | $$  \__/| $$  \ $$
| $$__  $$| $$| $$__  $$| $$ /$$$$| $$  | $$
| $$  \ $$| $$| $$  \ $$| $$|_  $$| $$  | $$
| $$  | $$| $$| $$  | $$| $$  \ $$| $$  | $$
| $$$$$$$/| $$| $$  | $$|  $$$$$$/|  $$$$$$/
|_______/ |__/|__/  |__/ \______/  \______/ 

```
# binGO-CLI

![Bingo Demo](bingo_demo.gif)

A terminal bingo game written in Go for quick fun in meetings. Reads phrases from `buzzwords.csv` and displays a 3x3 bingo board. Supports single-player (no dependencies) and multiplayer via WebSocket connection to a remote binGO server.

**Server code has moved to [github.com/jkMLnop/binGO](https://github.com/jkMLnop/binGO).** This repo is a pure CLI client.

## Requirements

**To play immediately:** Just download a prebuilt binary (see Quick Start)—no setup needed!

**To build from source:**
- Go 1.25+ (the project `go.mod` currently specifies `go 1.25.3`)

## Quick Start - Prebuilt Binaries

Pre-compiled binaries are available in GitHub Releases for:
- **macOS Intel (base)**: `binGO-CLI-intel-mac`
- **Linux x86_64**: `binGO-CLI-linux`

### Download & Run

1. **Download** the binary for your platform from the [latest release](https://github.com/jkMLnop/binGO-CLI/releases):
   ```bash
   # macOS Intel
   wget https://github.com/jkMLnop/binGO-CLI/releases/latest/download/binGO-CLI-intel-mac
   chmod +x binGO-CLI-intel-mac
   ./binGO-CLI-intel-mac -mode standalone
   
   # Linux x86_64
   wget https://github.com/jkMLnop/binGO-CLI/releases/latest/download/binGO-CLI-linux
   chmod +x binGO-CLI-linux
   ./binGO-CLI-linux -mode standalone
   ```

2. **Or download manually:**
   - Visit [binGO-CLI Releases](https://github.com/jkMLnop/binGO-CLI/releases)
   - Download the binary for your OS
   - `chmod +x` the downloaded file
   - Run it: `./binGO-CLI-intel-mac -mode standalone` (or `-linux` for Linux)

## Build from Source

```bash
# Build
cd /path/to/binGO-CLI
go build -o binGO-CLI
chmod +x binGO-CLI

# Or run directly without building
go run . -mode standalone
```

## Modes

- **`standalone`** (default): Single-player game, no networking
  ```bash
  ./binGO-CLI -mode standalone
  ```

- **`client`**: Connect to a remote binGO server and play multiplayer
  ```bash
  ./binGO-CLI -mode client -server localhost:8080
  ./binGO-CLI -mode client -server bingo-server.fly.dev -code BINGO-XXXXX
  ```

## Usage

### Standalone Mode
- The program reads `buzzwords.csv` in the project root and uses the first column of each row.
- Each cell displays its numpad number (1-9) in the top-left, with the phrase centered below.
- Enter a number 1-9 to mark the corresponding cell; enter `q` to quit.
- Win by marking three in a row (horizontal, vertical, or diagonal).

### Multiplayer Mode (Client)

Connect to any running binGO server (local, LAN, or cloud):

```bash
./binGO-CLI -mode client -server <server-address>:8080
./binGO-CLI -mode client -server bingo-server.fly.dev -code BINGO-XXXXX
```

## Architecture

```
binGO-CLI/
├── bin.go                      # Main entry point & CLI modes
├── client/                     # Multiplayer CLI client
│   ├── auth.go                 # Local session management (token storage, username prompts)
│   ├── auth_test.go            # Auth tests
│   ├── player.go               # Connection, board sync, input handling
│   ├── display.go              # Client-side UI rendering
│   └── types.go                # Client message types
├── server/                     # Multiplayer WebSocket server
│   ├── auth.go                 # JWT token generation & validation (IP-bound)
│   ├── auth_test.go            # Auth unit tests
│   ├── server.go               # WebSocket handler, game coordination
│   ├── server_test.go          # Server unit tests
│   ├── game.go                 # Player & Game structs with thread-safe operations
│   ├── api.go                  # REST API endpoints (game lookup, leaderboard, status)
│   ├── api_test.go             # API endpoint tests
│   ├── db.go                   # Database integration & helpers
│   ├── player_db.go            # Player database tracking
│   ├── types.go                # Message types
│   ├── utils.go                # Utility functions
│   └── utils_test.go           # Utility tests
├── shared/                     # Shared game logic (all modes)
│   ├── board.go                # Board management, cell marking, win detection
│   ├── board_test.go           # Board unit tests
│   ├── display.go              # Terminal rendering and formatting
│   ├── display_test.go         # Display unit tests
│   ├── types.go                # Type definitions
│   ├── utils.go                # CSV loading utilities
│   └── utils_test.go           # Utility tests
├── standalone/                 # Single-player mode
│   └── player.go               # Game loop & input handling
├── db/                         # Database layer (Phase 7.5)
│   ├── store.go                # GameStore interface (abstract DB operations)
│   ├── sqlite.go               # SQLite implementation with full CRUD
│   └── sqlite_test.go          # Database unit tests
├── docs/                       # Documentation
│   ├── ROADMAP.md              # Development phases and roadmap
│   ├── DEPLOYMENT.md           # Cloud deployment guide (Fly.io)
│   └── MONITORING_SETUP.md     # Monitoring & observability setup
├── tests/                      # Integration & regression tests
│   ├── multiplayer_test.go         # 12+ multiplayer integration tests
│   ├── db_integration_test.go      # 7 database persistence tests
│   ├── container_e2e_test.go       # Testcontainers-based E2E tests
│   ├── container_regression_test.go # Container regression suite
│   ├── full_system_load_test.go    # E2E load test (requires docker-compose)
│   ├── README.md                   # Test documentation
│   └── REGRESSION_TESTS.md         # Manual regression test checklist
├── dagger/                     # Dagger CI/CD pipeline (separate Go module)
│   ├── main.go                 # Pipeline: test, build, publish, deploy, release
│   └── main_test.go            # Pipeline unit tests
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions (thin trigger → Dagger)
├── .lefthook.yml               # Git pre-push hooks (enforces tests before push)
├── Dockerfile                  # Multi-stage Alpine build
├── docker-compose.yml          # bingo-server + Prometheus + Grafana
├── fly.toml                    # Fly.io production config
├── fly.staging.toml            # Fly.io staging config
├── prometheus.yml              # Prometheus scrape config
├── buzzwords.csv               # Default sample dataset
├── buzzwords_full.csv          # Extended buzzword set
├── bingo.db                    # SQLite database (created with -db flag)
├── go.mod                      # Go module dependencies
├── go.sum                       # Dependency checksums
├── CHANGELOG.md                # Version history
├── LICENSE                     # MIT license
└── binGO*                      # Prebuilt binaries (macOS, Linux)
    ├── binGO                   # Apple Silicon binary (arm64)
    ├── binGO-CLI-intel-mac     # Intel Mac binary (amd64)
    └── binGO-CLI-linux         # Linux binary (amd64)
```

## Data
`buzzwords.csv` is included as a sample dataset. If you replace it with your own file, keep the same CSV format (one phrase per row, first column used).

## Testing

```bash
# Unit tests (fast, no Docker)
go test ./...

# Unit + integration tests
go test -tags=integration ./tests -v

# Container regression tests (Docker must be running)
go test -tags=container -timeout=10m ./tests -v

# Run the same pipeline CI uses (via Dagger)
cd dagger && go run . test
```

See [tests/README.md](tests/README.md) for full test documentation.

## CI/CD

All pipeline logic lives in `dagger/main.go` (a separate Go module). GitHub Actions (`.github/workflows/ci.yml`) is a thin trigger layer that calls Dagger functions. [Lefthook](.lefthook.yml) enforces the same pipeline locally before every `git push`.

| Trigger | Pipeline |
|---------|----------|
| PR to `main` | `dagger test` (unit + integration) |
| Push to `main` | `dagger test` → build Docker image → publish to ghcr.io → deploy to staging (Fly.io) |
| Tag `v*` | Full pipeline → deploy to production (Fly.io) + GitHub Release with cross-compiled binaries |

### Creating a Release

Tag a commit and push — the pipeline runs automatically:
```bash
git tag v2.0.0
git push origin v2.0.0
```

GitHub Actions will run the full pipeline and create a GitHub Release with cross-compiled binaries (macOS Intel, Linux x86_64) and SHA256 checksums.

### Local pre-push enforcement (Lefthook)

```bash
go install github.com/evilmartians/lefthook@latest && lefthook install
# Every git push now runs unit+integration tests (via Dagger) and container tests automatically
git push --no-verify  # bypass in emergencies
```

## Project Roadmap

See [docs/ROADMAP.md](docs/ROADMAP.md) for the development roadmap and upcoming phases.
