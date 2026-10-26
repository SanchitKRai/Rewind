# Squad Charter: UI & CLI

**Tech Lead:** [Name]  
**Mission:** Build the developer interface — terminal CLI for capture/replay/fork workflows and a web-based visual debugger for step inspection, state diffs, and causal navigation.

---

## OKRs (Day 5 Outcomes)

| OKR | Metric | Target |
|-----|--------|--------|
| **OKR 1: CLI Workflow Coverage** | `rewind capture`, `rewind replay`, `rewind fork`, `rewind export-ci` all functional | 4/4 commands working end-to-end |
| **OKR 2: Visual Debugger Usability** | Time for new user to: load run → find failure → fork → edit → verify fix | <5 minutes (measured in Day 5 user test) |
| **OKR 3: CI Export Fidelity** | Exported GitHub Actions workflow reproduces failure in clean CI runner | 100% reproduction on 3 test failures |

---

## Dependencies on Other Squads

| Need From | Squad | Required By | Details |
|-----------|-------|-------------|---------|
| Capture CLI flags + config schema | Capture | Day 3 EOD | `--capture-syscalls`, `--output-dir`, `--format` |
| Replay query API (run, step, ancestors, divergence) | Replay Engine | Day 3 EOD | Contract: `contracts/log-to-replay.md` |
| State diff format + sandbox status events | State | Day 3 EOD | Contract: `contracts/state-to-ui.md` |
| DevEx: packaging, installers, autocomplete | DevEx & Launch | Day 4 EOD | Homebrew, npm, pip, cargo; shell completions |

---

## Top 3 Risks + Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| **Web debugger too heavy for 5-day build** | Medium | High | Scope to read-only view first (inspect, diff, navigate); fork/edit in CLI only; web fork as stretch |
| **State diff visualization unclear for complex state (DB, sockets)** | High | Medium | Spike Day 2: design diff views for each state type; use existing tools (sqldiff, pcap diff) as reference |
| **CLI/TUI vs Web feature parity confusion** | Low | Medium | Explicitly document: CLI = automation/CI, Web = exploration/debugging; no parity required |

---

## Day 1–2 Spike Needed?

**YES** — **Spike: Visual Debugger Tech Stack** (Owner: UI Tech Lead, Due: Day 2 EOD)
- Compare: `Textual` (Python TUI) vs `React` + `Vite` + `WebSocket` vs `Tauri` (Rust + WebView)
- Criteria: dev velocity, distribution size, terminal embedding, WebSocket streaming perf
- Deliverable: Working "load run → show steps" prototype in each → `docs/ui-stack-decision.md`