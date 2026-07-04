# Project Roadmap

> **Archive notice:** The roadmap has moved to the
> [binGO](https://github.com/jkMLnop/binGO) repository along with all server,
> web client, and platform code. This file is retained in binGO-CLI as a
> historical reference only.
>
> All server-side roadmap phases (Kubernetes, rooms, bets, AI agents, etc.)
> are now tracked in the binGO repository.

## CLI-Only Roadmap

- [ ] **Phase CLI-1**: Enhanced standalone mode — configurable board size, custom word lists, scoring/stats
- [ ] **Phase CLI-2**: TUI interface (bubbletea or similar) for richer terminal experience
- [ ] **Phase CLI-3**: Cross-compile release automation for macOS (Intel + Apple Silicon), Linux, Windows

##### Phase 16.1: Playwright Dashboard Smoke (Local + Staging)
**Goal:** Add browser smoke tests that prove Grafana is usable end-to-end.

- [ ] Add Playwright spec file(s), e.g. `web-client/e2e/grafana-smoke.spec.js`.
- [ ] Test flow: open Grafana login, authenticate, load dashboard, assert key panels/titles render, assert no datasource error banners.
- [ ] Add script(s) in `web-client/package.json`:
  - `smoke:grafana:local`
  - `smoke:grafana:staging`
- [ ] Add artifact retention for trace/screenshot on failure.

##### Phase 16.2: CI Wiring + Incident Hooks
**Goal:** Run Grafana smoke where web smoke already runs and auto-open issues on failures.

- [ ] Extend `.github/workflows/dev-smoke.yml` to run local Grafana smoke after stack readiness.
- [ ] Extend staging smoke path in `.github/workflows/ci.yml` with Grafana smoke and failure artifacts.
- [ ] Optional: add scheduled prod synthetic workflow for read-only Grafana checks.
- [ ] On failure, auto-create issue with run link + screenshot/trace pointers (same pattern as current staging/prod smoke failures).

##### Phase 16.3: Regression Mapping
**Goal:** Track observability UI checks in the same regression framework as game flows.

- [ ] Add a "Grafana dashboard smoke" section to `tests/REGRESSION_TESTS.md` and mark automated ownership.
- [ ] Keep only manual UX judgment items in the checklist; migrate deterministic checks to Playwright.

---

## Deferred / Maybe Never

#### Phase 9.6: In-Game Chat
**Goal:** Let players send free-form text messages to everyone in the game during play.

Deferred — the existing in-game bet system (`bet: <player> wins`) already provides structured social interaction. Free-form chat may add noise without much value for a bingo game. Revisit if users ask for it.

- `say <message>` command → `chat` WebSocket action → `chat_message` broadcast
- Rate-limit: 5 messages / 10 s per player
- Display: `💬 <username>: <message>` inline below board, scrolls away on next redraw
