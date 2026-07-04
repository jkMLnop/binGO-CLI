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

Pre-compiled binaries are available in [GitHub Releases](https://github.com/jkMLnop/binGO-CLI/releases).

Download the binary for your platform, make it executable, and run:

```bash
chmod +x ./binGO-CLI-*
./binGO-CLI-* -mode standalone
```

| Platform | Binary name |
|----------|-------------|
| macOS Apple Silicon | `binGO` |
| macOS Intel | `binGO-CLI-intel-mac` |
| Linux x86_64 | `binGO-CLI-linux` |

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
├── docs/                       # Documentation
│   ├── ROADMAP.md              # CLI-only development roadmap
│   └── DEVOPS.md               # Build and release notes
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions: go test + go build
├── buzzwords.csv               # Default sample dataset
├── CHANGELOG.md                # Version history (archived)
├── LICENSE                     # MIT license
├── go.mod                      # Go module
└── go.sum                      # Dependency checksums
```

## Data
`buzzwords.csv` is included as a sample dataset. If you replace it with your own file, keep the same CSV format (one phrase per row, first column used).

## Testing

```bash
# Unit tests
go test ./...
```

## CI

A simple GitHub Actions workflow (`.github/workflows/ci.yml`) runs `go test ./...`
and `go build` on every push/PR to `main`. No secrets, no deployment, no containers.

### Creating a Release

Build the binary manually and attach to a GitHub Release:

```bash
go build -ldflags "-X main.version=vX.Y.Z" -o binGO-CLI .
```

Prebuilt binaries are attached to [GitHub Releases](https://github.com/jkMLnop/binGO-CLI/releases).

## Roadmap

See [docs/ROADMAP.md](docs/ROADMAP.md) for the CLI-only development roadmap.
